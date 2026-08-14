# Publish/Subscribe (Pub/Sub)

Publish/subscribe is a messaging pattern in which producers publish messages to a topic without needing to know which consumers will receive them. Subscribers register interest in one or more topics and receive messages published to those topics.

This decouples producers from consumers. A single event can be delivered to multiple independent subscribers, allowing systems to react to the same event in different ways without tightly coupling their code or deployment lifecycles.

Pub/sub is commonly used for event-driven systems, notifications, integration between services, and asynchronous processing. Important concepts include topics, publishers, subscribers, message delivery guarantees, retries, ordering, and handling duplicate messages.

Examples of managed pub/sub systems include Google Cloud Pub/Sub and Amazon SNS.

Return to [Platform Engineering](./README.md).
