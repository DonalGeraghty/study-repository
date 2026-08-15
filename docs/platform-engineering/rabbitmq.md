# RabbitMQ

RabbitMQ is a message broker. Producers publish messages to exchanges, exchanges route them through bindings, and queues retain them for consumers. This separation supports work queues, routing, fan-out, and request decoupling without making the producer know every consumer.

## Quick Refresh

```text
producer -> exchange --binding--> queue -> consumer
                       \---------> queue -> consumer
```

| Concept | Responsibility |
| --- | --- |
| Producer | Publishes a message and routing information |
| Exchange | Applies routing rules; it does not normally store messages |
| Binding | Connects an exchange to a queue with an optional routing rule |
| Queue | Buffers messages until a consumer processes them |
| Consumer | Receives, handles, and acknowledges deliveries |
| Virtual host | Isolates names, permissions, exchanges, and queues within a broker |

## Exchange Types

| Type | Routing behaviour | Typical use |
| --- | --- | --- |
| Direct | Exact routing-key match | Route a command or category to one or more queues |
| Topic | Pattern match using dot-separated words | Route events such as `order.*` or `order.eu.*` |
| Fanout | Send to every bound queue | Broadcast an event to independent consumers |
| Headers | Match selected message headers | Routing that does not fit a string key |

The default exchange routes to a queue whose name exactly matches the routing key. It is convenient for simple work queues but does not replace an explicit topology when several capabilities evolve independently.

## Worked Example: Order Events

Suppose inventory and email both react to `order.placed`:

```text
                                -> inventory.orders queue
order producer -> orders topic -|
                  exchange      -> email.orders queue
```

The producer publishes once. Each capability owns a queue, so one consumer being offline does not remove the other consumer's copy.

```python
import json
import pika

connection = pika.BlockingConnection(
    pika.ConnectionParameters(host="rabbitmq", heartbeat=30)
)
channel = connection.channel()

channel.exchange_declare(
    exchange="orders",
    exchange_type="topic",
    durable=True,
)
channel.confirm_delivery()

event = {
    "eventId": "evt-730",
    "eventType": "order.placed.v1",
    "orderId": "ord-730",
}

channel.basic_publish(
    exchange="orders",
    routing_key="order.placed",
    body=json.dumps(event).encode(),
    properties=pika.BasicProperties(
        content_type="application/json",
        delivery_mode=2,
        message_id=event["eventId"],
    ),
    mandatory=True,
)
connection.close()
```

Durable exchanges and queues plus persistent messages improve survival across broker restarts; they do not remove the need for publisher confirms, replicated broker storage, backups where appropriate, and tested failure handling. `mandatory=True` makes an unroutable publish visible to the publisher when return handling is configured.

## Consumer Acknowledgements

```python
def handle(channel, method, properties, body):
    try:
        event = json.loads(body)
        reserve_inventory_idempotently(event)
    except TemporaryDependencyError:
        channel.basic_nack(method.delivery_tag, requeue=True)
        return
    except (json.JSONDecodeError, InvalidEventError):
        channel.basic_nack(method.delivery_tag, requeue=False)
        return

    channel.basic_ack(method.delivery_tag)


channel.basic_qos(prefetch_count=10)
channel.basic_consume(
    queue="inventory.orders",
    on_message_callback=handle,
    auto_ack=False,
)
```

Manual acknowledgement places the success boundary after the side effect. If the connection closes first, RabbitMQ can redeliver, so `reserve_inventory_idempotently` must tolerate the same event more than once.

Prefetch bounds the number of unacknowledged deliveries assigned to a consumer. A value that is too high can distribute work unfairly or consume too much memory; one that is too low can limit throughput. Measure with realistic message duration and consumer count.

## Retries and Dead-Lettering

Immediately requeueing a persistent failure can create a hot loop. A common topology dead-letters a failed message to a retry queue with an expiration, then routes it back after the delay. After a bounded number of attempts, route it to a final dead-letter queue for diagnosis.

```text
work queue --failure--> retry exchange -> delayed retry queue
    ^                                      |
    |---------- retry after TTL -----------|

work queue --attempts exhausted--> dead-letter queue
```

Track attempt metadata without trusting an unbounded client-supplied header. Invalid payloads should normally go directly to a diagnostic path rather than consume every retry interval.

## Reliability Boundaries

- **Publisher confirms** tell a producer that the broker accepted responsibility for a publish.
- **Consumer acknowledgements** tell the broker that a delivery can be removed from the queue.
- **Durability and persistence** influence restart survival.
- **Quorum queues** replicate queue state across broker nodes for stronger availability and data safety than a single node.
- **Idempotency** protects application side effects from redelivery.

These mechanisms protect different gaps and cannot substitute for one another.

## Operations and Security

- Give applications separate users and least-privilege permissions within appropriate virtual hosts.
- Encrypt network traffic where the trust boundary requires it and protect the management interface.
- Monitor connection and channel counts, unacknowledged messages, publish/ack rates, oldest message age, disk and memory alarms, and dead letters.
- Use bounded message sizes and keep large files in object storage with a controlled reference in the message.
- Handle blocked publishers and broker flow control rather than retrying without limits.
- Test broker restart, node loss, consumer crash, network interruption, and backlog recovery.

## Common Failure Modes

- publishing to an exchange with no matching binding and ignoring returned messages;
- enabling automatic acknowledgement before work completes;
- requeueing permanent failures forever;
- assuming persistent messages on one broker node constitute a recovery plan;
- sharing one queue between consumers that each need their own copy;
- treating message order as globally guaranteed while using parallel consumers;
- opening a new connection for every message.

## Practice

Sketch a topology for image processing: one command should be handled by one worker, while completion should notify billing and analytics independently. Name the exchanges, routing keys, queues, acknowledgement point, retry path, and idempotency key.

## Official References

- [Exchanges and bindings](https://www.rabbitmq.com/docs/exchanges)
- [Consumer acknowledgements and publisher confirms](https://www.rabbitmq.com/docs/confirms)
- [Reliability guide](https://www.rabbitmq.com/docs/reliability)
- [Queues](https://www.rabbitmq.com/docs/queues)

## Related Guides

- [Publish/Subscribe](./pub-sub.md)
- [Amazon SQS](./amazon-sqs.md)
- [Amazon SNS](./amazon-sns.md)
- [Docker](./docker.md)

Return to [Platform Engineering](./README.md).
