# Email and SMTP

SMTP transfers email between clients and mail servers. Application code usually authenticates to a provider, submits a message, and lets the provider handle onward delivery.

## Message Flow

```text
application -> authenticated SMTP submission -> mail provider -> recipient server
```

Construct messages with the standard email types rather than hand-building headers. Validate recipients, set an intentional plain-text or HTML content type, and prevent untrusted values from injecting headers or misleading links.

## Credentials and Delivery

- Store SMTP credentials outside source and rotate them when exposed.
- Use encrypted transport and verify server certificates.
- Apply connection and send timeouts.
- Distinguish temporary failures from permanent recipient or authentication failures.
- Avoid retrying indefinitely or sending duplicates after an ambiguous timeout.
- Log delivery identifiers and error categories without recording credentials or sensitive message bodies.

For larger or user-facing systems, use a queue or outbox so request latency and transient mail failure do not control the main transaction. Define rate limits, bounce handling, suppression, monitoring, and legal or consent requirements appropriate to the messages.

## Testing

Unit-test message composition and recipient selection without sending real mail. Use a local or sandbox SMTP server for integration tests, then keep a narrow opt-in check for the real provider configuration.

## Project Connections

The Playwright CEX crawler builds a text email with Python's standard email library and submits it through `smtplib` using credentials loaded from environment configuration.

## Related Guides

- [Python](../languages/python.md)
- [Testing](../../quality-engineering/testing.md)
- [GitHub Actions](../../platform-engineering/ci-cd/github-actions.md)

Return to [External Integrations](./README.md).
