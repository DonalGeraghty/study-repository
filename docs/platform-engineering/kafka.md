---
tags:
  - platform-engineering
---

# Apache Kafka

Apache Kafka is a distributed, partitioned, replicated event log. Producers append records to topics, brokers persist them in order per partition, and consumers read at their own pace by tracking an offset. Unlike a queue that deletes a message once processed, Kafka retains records for a configured period so multiple independent consumer groups can replay the same log at different positions.

## Quick Refresh

```text
producer -> topic "orders" (3 partitions)
              partition 0: [0][1][2][3]---> consumer group "inventory" (offset 2)
              partition 1: [0][1][2]------> consumer group "inventory" (offset 1)
              partition 2: [0][1][2][3][4]-> consumer group "inventory" (offset 4)

                                          -> consumer group "analytics" (offset 0, replaying from start)
```

| Concept | Responsibility |
| --- | --- |
| Topic | Named, partitioned append-only log |
| Partition | Ordered, independently replicated shard of a topic |
| Offset | A consumer's position within one partition |
| Broker | A server that stores partitions and serves reads and writes |
| Replica | A copy of a partition on another broker |
| Consumer group | A set of consumers that divide a topic's partitions between them |
| Retention | How long records remain available, by time or size, regardless of consumption |

A partition is Kafka's unit of order and parallelism. Order is guaranteed within a partition, never across an entire topic.

## Producers and Partitioning

A producer chooses a partition explicitly or through its configured partitioner. Keys commonly determine partition placement; keyless distribution depends on the client and configuration and may use sticky batching rather than round robin:

```python
from confluent_kafka import Producer

producer = Producer({
    "bootstrap.servers": "kafka-1:9092,kafka-2:9092",
    "acks": "all",
    "enable.idempotence": True,
    "linger.ms": 5,
})


def on_delivery(err, msg):
    if err is not None:
        log_delivery_failure(err, msg.key())


producer.produce(
    topic="orders",
    key=str(order_id).encode(),
    value=json.dumps(event).encode(),
    on_delivery=on_delivery,
)
producer.poll(0)
producer.flush()
```

Keying by `order_id` sends every event for that order to the same partition, which preserves relative order for that entity while unrelated orders process in parallel across other partitions. Choosing a key with too few distinct values concentrates load on a small number of partitions; choosing one with no relationship to the data loses ordering guarantees consumers may assume exist.

`acks=all` waits for the record to be written to every in-sync replica, not just the partition leader. `enable.idempotence` prevents a producer retry from creating duplicate records within one partition, though it does not make the caller's overall operation exactly-once.

## Consumers and Consumer Groups

```python
import json
from confluent_kafka import Consumer, KafkaException

consumer = Consumer({
    "bootstrap.servers": "kafka-1:9092,kafka-2:9092",
    "group.id": "inventory",
    "enable.auto.commit": False,
    "auto.offset.reset": "earliest",
})
consumer.subscribe(["orders"])

try:
    while True:
        msg = consumer.poll(1.0)
        if msg is None:
            continue
        if msg.error():
            raise KafkaException(msg.error())

        event = json.loads(msg.value())
        reserve_inventory_idempotently(event)
        consumer.commit(message=msg, asynchronous=False)
finally:
    consumer.close()
```

This sketch assumes the application provides `reserve_inventory_idempotently`. An unhandled processing failure stops the loop without committing that record. Restart then resumes from the last committed position, subject to retention. A production supervisor needs bounded restart/backoff and a poison-message policy; stopping is deliberately simpler here than implementing a partition-aware retry scheduler.

**Polling position and committed offset are different.** Polling advances the current position even when processing fails. Merely skipping a commit and calling `poll()` again does not request the same record. Committing a later record in that partition can skip the failed work on restart. A continuing consumer must explicitly coordinate retry, pause/seek behaviour, and contiguous completed offsets.

Kafka assigns each partition to exactly one consumer within a group, so a group's parallelism is bounded by its partition count; a fourth consumer in a three-partition group sits idle. Two groups reading the same topic each maintain their own offsets and never compete with each other, which is how `inventory` and `analytics` can consume the same events independently, including at different retention-bound starting points.

Committing after the side effect, not before, matches the same at-least-once discipline used with other brokers: a crash between processing and commit causes redelivery, so `reserve_inventory_idempotently` must tolerate the same event more than once.

## Rebalancing

When a consumer joins, leaves, or is considered dead by a missed heartbeat, the group rebalances and partitions move between consumers:

```text
group "inventory": 3 partitions, 2 consumers
consumer A crashes
      |
group coordinator detects missed heartbeat
      |
remaining consumer takes all 3 partitions
```

A rebalance pauses processing for the affected partitions while assignment settles. Long-running message handlers can exceed `max.poll.interval.ms` and be evicted as if they had died, triggering another rebalance. Keep per-record processing time predictable, or offload slow work and continue polling.

## Retries and Dead Letters

Kafka has no built-in per-message negative acknowledgement or broker-managed dead-letter queue. Retry and dead-letter handling are consumer responsibilities, commonly built with additional topics:

```text
orders -> consumer --failure--> orders.retry (with backoff, replayed by a delayed processor)
                    --attempts exhausted--> orders.dlq
```

Track attempt count in a record header or an external store, since replaying the original topic from the beginning is not a substitute for isolating a poison message. A dead-letter topic still needs an owner, alerting, and a replay procedure, exactly as with any other broker.

## Delivery Semantics and Exactly-Once

With at-most-once processing, committing the offset before processing can lose unprocessed records if the consumer crashes. Committing after processing provides at-least-once behaviour: a crash can cause redelivery and duplicate work.

Kafka's exactly-once semantics use transactional producers and `read_committed` consumers to make a read-process-write cycle atomic within Kafka. That guarantee does not automatically cover an external database write or outbound call. The external effect needs to participate in an appropriate transactional boundary or be protected by idempotency.

Idempotent consumers remain the practical default. Record the record's topic, partition, and offset (or an application event ID) under a uniqueness constraint alongside the business change, the same pattern used for other brokers.

## Replication and Durability

| Setting | Effect |
| --- | --- |
| Replication factor | Number of broker copies of each partition |
| `min.insync.replicas` | Minimum in-sync replicas required before an `acks=all` write succeeds |
| Leader election | A surviving in-sync replica becomes leader if the current leader fails |
| `acks` (producer) | `0` no wait, `1` leader only, `all` every in-sync replica |

A replication factor of three with `min.insync.replicas=2` tolerates one broker failure without blocking writes and without silently accepting writes that only reached a single node. Setting `min.insync.replicas` equal to the replication factor maximises durability at the cost of availability during a broker outage.

## Operations and Security

- Size partitions for target parallelism up front; increasing partition count later changes key-to-partition mapping and can break existing ordering assumptions.
- Set retention (`retention.ms`, `retention.bytes`) or compaction per topic based on whether consumers need a time-bounded stream or the latest value per key.
- Monitor consumer lag per partition, under-replicated partitions, request latency, and disk usage; a low message rate can hide a stalled consumer if lag is not measured directly.
- Authenticate with SASL or mTLS, encrypt traffic with TLS, and use ACLs to restrict which principals can produce or consume each topic.
- Keep schemas explicit and compatible (for example with a schema registry); an unannounced breaking field change reaches every consumer group, not just one.
- Test broker loss, leader election, consumer rebalance during deployment, and backlog recovery at expected peak volume.

## Common Failure Modes

- assuming ordering across an entire topic instead of within a partition;
- keying by a low-cardinality or unrelated value and creating hot or meaningless partitions;
- committing an offset before the corresponding side effect completes;
- writing slow, unbounded per-record handlers that trigger repeated rebalances;
- treating Kafka retention as a replacement for a dead-letter and replay strategy;
- increasing partition count without checking downstream ordering assumptions;
- running with `acks=1` or no `min.insync.replicas` floor and calling the result durable;
- letting an incompatible schema change reach consumers without a compatibility check.

## Worked Prediction: The Offset Gap

A partition's committed offset is `40`, meaning the next record to resume is record `40`. The consumer polls record `40`, processing fails, then incorrectly continues and successfully processes record `41`. What happens if it commits record `41`?

**Check your reasoning:** In this client, committing that message stores the next offset, `42`. A restarted consumer resumes there; record `40` has been skipped for the group even though its processing never succeeded. Not committing a failed record is insufficient when later commits pass it. Track contiguous completion within each partition, or stop/pause that partition until the failure has been handled deliberately.

For a second attempt, let the side effect for record `40` commit but crash before the offset commit. Replay can repeat the side effect, so the operation needs a durable idempotency key. Explain why Kafka transactions alone cannot make an unrelated email or database write exactly once.

## Interview Questions

> [!question] Interview Questions
> - For order events consumed independently by inventory, billing, and analytics, what partition key would you choose to give inventory strict per-order ordering?
> - How would consumer group boundaries let inventory and analytics process the same topic at completely different paces?
> - How would analytics replay the last 30 days of events after a bug fix, without affecting inventory's live processing?
> - How would you design a retry/dead-letter topic for a consumer, given Kafka has no built-in negative acknowledgement?

## Answer Notes

1. Use a stable order ID as the key so events for one order reach the same partition. Ordering is per partition, not across the topic; preserve processing order in the consumer and plan carefully for partition-count changes.

2. Give inventory and analytics different consumer group IDs so each tracks its own committed offsets. Members within one group share partitions; separate groups each consume the topic independently and can build different lag.

3. Confirm the events are still retained, then use a separate replay group or carefully reset only the stopped analytics group's offsets to the desired time. Make analytics effects replay-safe; inventory's group offsets remain independent.

4. Define bounded retries, backoff, failure metadata and a dead-letter destination. Coordinate writing the failure record with advancing the source offset to avoid loss, tolerate duplicate transfer, and decide whether skipping a failed event would violate ordering.

## Official References

- [Kafka documentation](https://kafka.apache.org/documentation/)
- [Consumer positions and committed offsets](https://docs.confluent.io/kafka/design/consumer-design.html)
- [Confluent Python client API](https://docs.confluent.io/platform/current/clients/confluent-kafka-python/html/index.html)
- [Consumer groups and rebalancing](https://kafka.apache.org/documentation/#intro_consumers)
- [Replication](https://kafka.apache.org/documentation/#replication)
- [Exactly-once semantics](https://kafka.apache.org/documentation/#semantics)

## Related Guides

- [Publish/Subscribe](./pub-sub.md)
- [RabbitMQ](./rabbitmq.md)
- [Amazon SNS](./amazon-sns.md)
- [Amazon SQS](./amazon-sqs.md)

Return to [Platform Engineering](./README.md).
