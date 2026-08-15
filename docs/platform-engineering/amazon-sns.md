# Amazon SNS

Amazon Simple Notification Service (SNS) is AWS's managed publish/subscribe service. A publisher sends one message to a topic, and SNS attempts delivery to each matching subscription. Common destinations include SQS queues, Lambda functions, HTTP/S endpoints, email, SMS, and mobile push.

## Quick Refresh

```text
                                    -> SQS queue -> inventory workers
order service -> SNS orders topic  -> SQS queue -> analytics workers
                                    -> Lambda    -> notification logic
```

| Concept | Purpose |
| --- | --- |
| Topic | Named publish endpoint and fan-out boundary |
| Subscription | Topic-to-destination delivery configuration |
| Filter policy | Selects messages for a subscription using attributes or body fields |
| Delivery policy | Controls retry behaviour for supported destination types |
| Dead-letter queue | Retains messages SNS could not deliver to a subscription |
| Message attribute | Typed metadata used for filtering or consumer context |

SNS decouples the publisher from the destination list. It does not make every destination durable: an SQS subscription retains a backlog, while an email or HTTP destination has different failure and replay behaviour.

## Worked Example: Publish an Order Event

Store the event in `order.json`:

```json
{
  "eventId": "evt-730",
  "eventType": "order.placed.v1",
  "orderId": "ord-730",
  "occurredAt": "2026-08-15T09:30:00Z"
}
```

Publish it with an attribute that subscriptions can filter:

```bash
aws sns publish \
  --topic-arn arn:aws:sns:eu-west-1:123456789012:orders \
  --message file://order.json \
  --message-attributes '{
    "eventType": {
      "DataType": "String",
      "StringValue": "order.placed.v1"
    }
  }'
```

An inventory subscription could use this filter policy:

```json
{
  "eventType": ["order.placed.v1", "order.cancelled.v1"]
}
```

Filtering saves consumers from receiving irrelevant events, but filter changes are production behaviour and need review, tests, and monitoring. A missing or misspelled attribute can silently prevent an expected delivery.

## SNS with SQS

Combining SNS and SQS is a common durable fan-out pattern:

1. The producer publishes once to an SNS topic.
2. Each capability owns an SQS queue and an SNS subscription.
3. SNS places a copy in every matching queue.
4. Each capability processes and retries independently.

The SQS queue policy must allow the intended SNS topic to send messages. Restrict the policy with the topic ARN rather than allowing every topic in an account or every AWS principal.

By default, an SQS delivery can contain an SNS envelope with the original message and SNS metadata. Raw message delivery removes that envelope. Select one contract intentionally and test consumers against it; changing the setting changes the body they parse.

## Standard and FIFO Topics

Standard topics prioritise high throughput and broad endpoint support. Delivery can be duplicated and ordering is not a global guarantee, so subscribers must be idempotent and order-independent unless the domain supplies another mechanism.

FIFO topics support ordered, deduplicated message groups for compatible workflows. They require group and deduplication design and have different constraints. Even where SNS or SQS deduplicates a publish, the consumer's database update or external call still needs an application-level idempotency strategy.

## Reliability and Failure Handling

- Give the publisher a bounded request timeout and classify retryable AWS errors.
- Reuse the same application event ID when retrying an ambiguous publish.
- Configure a dead-letter queue per subscription where failed delivery must be investigated.
- Monitor delivery failures for each protocol and destination.
- Keep consumers idempotent because retries and duplicate delivery can occur.
- Validate that every production subscription exists after infrastructure changes.

An SNS dead-letter queue captures a failure to deliver to the subscribed endpoint. It is distinct from the SQS consumer's redrive policy, which handles a message successfully delivered to SQS but repeatedly failing during application processing.

## Security

- Restrict who may publish, subscribe, change topic attributes, and read configuration.
- Use topic and queue policies with specific principals and source resources.
- Encrypt in transit and use an appropriate KMS key when server-side encryption is required.
- Do not put secrets or unnecessary personal data in message attributes, bodies, logs, or email/SMS notifications.
- Protect against confused-deputy access with source-account and source-ARN conditions where applicable.

## Common Failure Modes

- assuming an HTTP, email, or Lambda subscriber behaves like a durable queue;
- subscribing two competing workers directly when each needs controlled backlog and visibility;
- changing raw message delivery without updating the consumer contract;
- adding a filter that excludes messages with missing attributes;
- granting a queue permission to every SNS topic;
- monitoring successful publishes but not per-subscription delivery failures;
- treating a notification sent to a user as proof that the user received or acted on it.

## Practice

Design an `account.security-alert.v1` topic with subscriptions for email delivery, an audit queue, and fraud analysis. Decide which subscriptions require a durable queue, which attributes may be filtered, what data must not enter the message, and where delivery versus processing failures are dead-lettered.

## Official References

- [Amazon SNS overview](https://docs.aws.amazon.com/sns/latest/dg/welcome.html)
- [Applying a subscription filter policy](https://docs.aws.amazon.com/sns/latest/dg/message-filtering-apply.html)
- [FIFO message grouping](https://docs.aws.amazon.com/sns/latest/dg/fifo-message-grouping.html)
- [SNS dead-letter queues](https://docs.aws.amazon.com/sns/latest/dg/sns-dead-letter-queues.html)

## Related Guides

- [Amazon SQS](./amazon-sqs.md)
- [Publish/Subscribe](./pub-sub.md)
- [Amazon Web Services](./cloud/aws.md)
- [Encryption](../engineering-foundations/encryption.md)

Return to [Platform Engineering](./README.md).
