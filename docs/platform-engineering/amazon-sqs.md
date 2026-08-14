# Amazon SQS

Amazon Simple Queue Service (SQS) is AWS's fully managed message queue service. It allows one part of a system to place messages onto a queue so that another part can process them later, which helps decouple services and handle work asynchronously.

Unlike publish/subscribe systems such as Amazon SNS, an SQS message is normally consumed by a worker rather than broadcast to every subscriber. This makes SQS useful for background jobs, buffering spikes in traffic, retryable processing, and communication between services that do not need to run at the same time.

SQS supports two main queue types: **Standard queues**, which prioritise scalability and at-least-once delivery, and **FIFO queues**, which provide ordered processing and stronger duplicate-handling guarantees.

A common AWS architecture combines **SNS + SQS**: SNS publishes an event to multiple subscribers, while separate SQS queues give each consuming service its own durable backlog of work.
