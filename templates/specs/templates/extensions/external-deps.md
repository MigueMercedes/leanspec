## External dependencies

> Extension: external-deps. Enabled when the feature relies on third-party APIs, webhooks, or billing providers.

For each external integration affected (Stripe, Twilio, Auth0, AWS services, OpenAI, Sentry, etc.):

- **Provider + endpoint(s)**: exact API path or SDK call
- **Failure modes**: what does 5xx / 429 / 403 / network timeout / partial response look like? How do you handle each?
- **Retries**: which retries with which backoff. Idempotency key strategy if applicable.
- **Circuit breaker**: trip condition if applicable
- **Cost**: $ per request / per event. Estimated monthly cost at expected scale.
- **Rate limits**: provider's published limit + your own internal throttling
- **Sandbox/staging**: how to test without hitting production. Provider-specific test mode (Stripe test cards, Twilio test credentials, etc.)
- **Webhook idempotence** (if receiving): duplicate / out-of-order delivery handling
