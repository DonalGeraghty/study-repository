# Amazon SQS

Amazon Simple Queue Service (SQS) is AWS's managed message queue. Producers place messages on a queue, and a worker from a competing-consumer pool processes each delivery. SQS is useful for background work, traffic buffering, and decoupling services that do not need to be available at the same time.

## Quick Refresh

```text
producer -> queue -> receive -> process -> delete
                       |          |
                       |          -> failure: visibility expires
                       |                       and message returns
                       -> message temporarily hidden
```

| Concept | Purpose |
| --- | --- |
| Visibility timeout | Temporarily hides a received message while it is processed |
| Receipt handle | Identifies one delivery attempt for delete or visibility changes |
| Long polling | Waits for available messages and reduces empty receives |
| Retention period | Limits how long an unprocessed message remains in the queue |
| Redrive policy | Moves repeatedly received messages to a dead-letter queue |
| Approximate receive count | Helps identify repeated processing attempts |

Receiving does not remove a message. The consumer deletes it only after successful processing.

## Standard and FIFO Queues

| Capability | Standard | FIFO |
| --- | --- | --- |
| Delivery | At least once; duplicates are possible | Deduplicates sends within its defined mechanism and interval |
| Ordering | Best effort | Ordered within a message group |
| Parallelism | Scales broadly | Scales across independent message groups |
| Name | Any valid queue name | Ends in `.fifo` |

FIFO delivery does not make a database write, email, or payment exactly once. Consumers must still make consequential side effects idempotent. Use a message group that represents the smallest entity requiring order; one group for all messages serialises the entire queue.

## Worked Consumer Example

```python
import json
import boto3

sqs = boto3.client("sqs", region_name="eu-west-1")
queue_url = "https://sqs.eu-west-1.amazonaws.com/123456789012/image-jobs"


def poll_once():
    response = sqs.receive_message(
        QueueUrl=queue_url,
        MaxNumberOfMessages=10,
        WaitTimeSeconds=20,
        VisibilityTimeout=60,
        MessageSystemAttributeNames=["ApproximateReceiveCount"],
    )

    for message in response.get("Messages", []):
        job = json.loads(message["Body"])

        try:
            process_image_idempotently(
                job_id=job["jobId"],
                source_key=job["sourceKey"],
            )
        except TemporaryDependencyError:
            # Do not delete. The message becomes visible for another attempt.
            continue

        sqs.delete_message(
            QueueUrl=queue_url,
            ReceiptHandle=message["ReceiptHandle"],
        )
```

Production code must also validate the payload, distinguish permanent failures, handle shutdown, expose metrics, and decide what to do when processing succeeds but deletion fails. Because that last case can cause redelivery, `jobId` protects the application side effect.

Use batch receive and batch delete where volume justifies it, but inspect failures per entry because a batch request can partially succeed.

## Visibility Timeout

Choose a visibility timeout longer than ordinary processing but not so long that a crashed worker delays recovery excessively. For genuinely variable long-running work, extend visibility while the worker is healthy:

```text
receive with 60 seconds
      |
process still healthy after 40 seconds
      |
extend visibility by another bounded interval
```

Extension is a lease, not proof of ownership. A delayed worker can continue after another worker receives the message, so the underlying operation still needs idempotency or concurrency control.

## Retries and Dead-Letter Queues

Configure a redrive policy with a source queue and a dead-letter queue (DLQ). The maximum receive count should allow useful transient recovery without hiding a permanent poison message for hours.

A DLQ needs:

- an alarm and an owner;
- enough retention for investigation;
- payload and schema diagnostics that do not leak secrets;
- a correction and redrive procedure;
- protection against replaying a side effect that already succeeded.

Redriving without correcting the cause only repeats the incident. Prefer application backoff or a deliberate delay-queue design when immediate visibility retry would create a hot loop.

## Producer Example

```python
sqs.send_message(
    QueueUrl=queue_url,
    MessageBody=json.dumps({
        "jobId": "job-730",
        "sourceKey": "uploads/730.png",
    }),
    MessageAttributes={
        "schemaVersion": {
            "DataType": "Number",
            "StringValue": "1",
        }
    },
)
```

SQS limits message size. Large files belong in durable object storage; send a validated reference, version, and integrity metadata rather than the bytes. Consider the failure gap between storing the file and sending its job message, and use an outbox or reconciliation process when losing the job is unacceptable.

## Security and Operations

- Grant producers `SendMessage` and consumers only the receive, visibility, and delete actions they need.
- Restrict queue policies to intended principals and source topics.
- Use encrypted transport and an appropriate server-side encryption configuration.
- Keep sensitive data out of bodies, attributes, logs, and dead-letter diagnostics unless its lifecycle is explicitly protected.
- Monitor oldest message age, visible and in-flight counts, empty receives, processing duration, deletion failures, and DLQ depth.
- Scale from backlog age and processing time, not raw message count alone.
- Define graceful shutdown so a worker stops polling, finishes or abandons in-flight work safely, and does not delete incomplete jobs.

## SQS with SNS

Use SQS alone when one worker pool should handle each job. Use SNS in front of separate SQS queues when several capabilities each require their own durable copy:

```text
job producer -> SQS -> worker pool

event producer -> SNS -> inventory SQS -> inventory workers
                      -> audit SQS     -> audit workers
```

## Common Failure Modes

- deleting before the business side effect commits;
- setting visibility shorter than normal processing and creating concurrent duplicates;
- setting visibility extremely long and delaying crash recovery;
- using one FIFO message group and accidentally serialising all work;
- retrying invalid messages until retention expires;
- creating a DLQ without alarms or a redrive procedure;
- assuming an empty short poll proves the queue has no messages;
- ignoring per-entry failure in batch operations.

## Practice

Design a queue for generating monthly reports. Reports take between ten seconds and ten minutes. Explain the message body, idempotency key, visibility extension, retry policy, DLQ, large output storage, worker shutdown, and scaling signal.

## Official References

- [Amazon SQS overview and message lifecycle](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html)
- [Visibility timeout](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-visibility-timeout.html)
- [Dead-letter queues](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-dead-letter-queues.html)
- [Long polling](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-long-polling.html)

## Related Guides

- [Amazon SNS](./amazon-sns.md)
- [Publish/Subscribe](./pub-sub.md)
- [RabbitMQ](./rabbitmq.md)
- [Amazon Web Services](./cloud/aws.md)

Return to [Platform Engineering](./README.md).
