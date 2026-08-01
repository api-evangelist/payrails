---
name: Accept a card payment
description: Authorize and capture a card payment through a Payrails workflow execution.
api: payrails
operations: [getoauthtoken, createexecution, authorizeaction, captureaction]
---

# Accept a card payment (Payrails)

Base URL: `https://api.payrails.io` (staging: `https://api.staging.payrails.io`).

## Steps
1. **Authenticate** — `getoauthtoken`: POST to `/auth/token/{clientId}` with your Client Secret to obtain a bearer access token. Tokens live for 1 hour — cache and reuse until `expires_in` elapses, then re-request. Send `Authorization: Bearer <token>` on every call.
2. **Start the payment** — `createexecution`: create a workflow execution for the order (amount, currency in ISO 4217, instrument/session). This runs the configured orchestration workflow.
3. **Authorize** — `authorizeaction`: authorize the payment on the routed provider. If the result is `HTTPRedirectRequired`/`FormRedirectRequired` (3DS or wallet), complete the redirect before proceeding.
4. **Capture** — `captureaction`: capture the authorized amount (immediately, or later for delayed capture).

## Rules
- **Idempotency**: send an idempotency key; on `request.timeout`/`service.unavailable` retry with the *same* key so authorize/capture never double-execute.
- **Result codes**: read `reason.result` — e.g. `InsufficientBalance`, `InvalidInstrument`, `FraudRisk`, `PayerAuthenticationRequired` (see `errors/payrails-decline-codes.yml`).
- **Errors**: API-level failures use the `errors[]` envelope with a machine `code` and a UUID `id` for support (see `errors/payrails-problem-types.yml`).
- **Async truth**: final status arrives via webhooks (`executionActionUpdated`) — verify the `X-Signature` HMAC-SHA256 header; do not treat the sync response as final.
