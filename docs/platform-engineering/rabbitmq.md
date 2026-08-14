# RabbitMQ

RabbitMQ is a message broker used to move messages between applications and services. Producers send messages to RabbitMQ, and consumers receive and process them asynchronously rather than requiring both sides to communicate directly at the same time.

RabbitMQ is commonly associated with queue-based messaging, but it also supports routing patterns that can resemble publish/subscribe. Messages are typically published to an exchange, which then routes them to one or more queues according to configured rules.

It is useful when systems need reliable asynchronous work, buffering between services, retries, routing, or controlled message consumption. Core concepts include producers, consumers, exchanges, queues, bindings, acknowledgements, and dead-letter queues.

Return to [Platform Engineering](./README.md).
