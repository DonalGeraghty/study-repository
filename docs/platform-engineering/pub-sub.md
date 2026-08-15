# Publish/Subscribe

Publish/subscribe is a messaging pattern in which producers publish events without naming the consumers. Subscribers express interest in a topic or routing rule and process their own copy. This decouples deployment and timing, but it moves complexity into contracts, delivery guarantees, retries, and operations.

## Quick Refresh

```text
                              -> inventory subscriber
order service -> orders topic -> email subscriber
                              -> analytics subscriber
```

| Concept | Question to answer |
| --- | --- |
| Topic | Which class of event does this channel carry? |
| Subscription | Which consumer owns this backlog and delivery state? |
| Acknowledgement | When is processing considered successful? |
| Retry | Which failures should be attempted again, and for how long? |
| Dead-letter destination | Where do repeatedly failing messages go? |
| Ordering key | Which messages genuinely need relative order? |
| Retention | How long can an offline consumer recover or replay? |

## Event Example

An event describes something that happened, not an instruction to one unnamed worker:

```json
{
  "eventId": "01J5A8Q2VX3J4M0T7W9Y6K2P1R",
  "eventType": "order.placed.v1",
  "occurredAt": "2026-08-15T09:30:00Z",
  "orderId": "ord-730",
  "customerId": "cus-42",
  "items": [
    {"sku": "kbd-uk", "quantity": 1}
  ],
  "total": {
    "amount": "75.00",
    "currency": "EUR"
  }
}
```

The stable event ID supports deduplication and tracing. The event type carries an explicit contract version. Timestamps include a time zone, and money is not represented as a binary floating-point value.

Do not publish an entire internal database row simply because it is available. Include enough stable information for consumers while considering privacy, retention, and future schema changes.

## Delivery Semantics

- **At-most-once:** a message may be lost but is not deliberately redelivered.
- **At-least-once:** a message is retried until acknowledged or exhausted, so duplicates are possible.
- **Exactly-once:** a narrow platform guarantee whose boundary must be examined carefully; it rarely makes every external side effect exactly once.

Design consumers as idempotent wherever practical. For example, record the event ID under a uniqueness constraint in the same database transaction as the business change:

```python
def handle_order_placed(event, database):
    with database.transaction() as tx:
        if tx.processed_events.exists(event["eventId"]):
            return

        tx.inventory.reserve(
            order_id=event["orderId"],
            items=event["items"],
        )
        tx.processed_events.add(event["eventId"])
```

The message should be acknowledged only after the transaction commits. If the process fails before acknowledgement, redelivery finds the recorded event and safely does nothing.

## Publishing Reliably

A dangerous gap exists when an application commits its database transaction and then fails before publishing the corresponding event. The transactional outbox pattern closes that gap:

```text
one database transaction:
    update order
    insert event into outbox

relay:
    read unpublished outbox rows
    publish event
    mark row published
```

The relay can publish a duplicate if it fails after sending but before marking the row. Consumers still need deduplication. Change-data capture can act as the relay when the chosen infrastructure and operational model justify it.

## Ordering and Parallelism

Global ordering restricts throughput and is often unnecessary. Ask which events must be ordered relative to each other. Events for one order or account may use the same ordering or partition key while unrelated entities process in parallel.

Even with ordered delivery, a retrying message may block later messages on the same key. Consumers should keep processing bounded, isolate poison messages, and expose backlog age as well as message count.

## Retries and Dead Letters

Retry transient failures such as a short dependency outage with bounded exponential backoff and jitter. Do not repeatedly retry invalid schemas, missing required data, or permanent authorisation failures without intervention.

A dead-letter queue or topic is a diagnostic holding area, not an automatic resolution. Record the reason, preserve correlation, alert an owner, and define how a corrected message is replayed without bypassing validation or causing duplicate side effects.

## Queue, Pub/Sub, or Event Log?

| Need | Often fits |
| --- | --- |
| One worker from a pool handles each job | Queue |
| Several independent capabilities react to one event | Pub/sub with one subscription per capability |
| Long-lived ordered history that consumers replay at their own position | Partitioned event log |
| Immediate request and response required by the caller | Synchronous API, possibly combined with later events |

Products can support more than one model. Verify their actual persistence, routing, ordering, and consumer semantics rather than relying on the product category.

## Testing and Operations

Test:

- schema compatibility and unknown fields;
- duplicates, reordered messages, and missing predecessors;
- consumer restart before and after committing its side effect;
- transient and permanent dependency failures;
- poison messages and dead-letter replay;
- backlog recovery at expected peak volume.

Monitor publish failures, delivery latency, oldest unprocessed age, retry count, dead-letter volume, consumer throughput, and end-to-end correlation. A low queue depth can hide a stuck message if age is not measured.

## Practice

Model an `invoice.paid` event consumed by fulfilment, email, and analytics. Decide the event contract, privacy boundary, subscription ownership, idempotency key, retry policy, ordering requirement, and replay procedure for each consumer.

## Related Guides

- [RabbitMQ](./rabbitmq.md)
- [Amazon SNS](./amazon-sns.md)
- [Amazon SQS](./amazon-sqs.md)
- [Google Cloud Platform](./cloud/gcp/README.md)
- [REST APIs](../quality-engineering/rest-api.md)

Return to [Platform Engineering](./README.md).
