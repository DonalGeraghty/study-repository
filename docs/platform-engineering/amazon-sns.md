# Amazon SNS

Amazon Simple Notification Service (SNS) is AWS's managed publish/subscribe messaging service. Publishers send messages to an SNS topic, and the topic can fan those messages out to multiple subscribers without the publisher needing to know who those subscribers are.

Subscribers can include services such as Amazon SQS queues, AWS Lambda functions, HTTP/S endpoints, email, and other supported destinations. A common AWS architecture is to publish an event to SNS and deliver a copy to one or more SQS queues so separate consumers can process the event independently and reliably.

SNS is most useful when one event needs to be distributed to multiple interested consumers. Amazon SQS is different: it is primarily a message queue for work that should be consumed from a queue, while Amazon EventBridge is an event bus designed for routing events between applications, AWS services, and integrations.

Return to [Platform Engineering](./README.md).
