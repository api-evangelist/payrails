---
name: Refund a payment
description: Locate a captured Payrails payment and issue a full or partial refund.
api: payrails
operations: [getoauthtoken, listpayments, getpayment, refundaction]
---

# Refund a payment (Payrails)

## Steps
1. **Authenticate** — `getoauthtoken` for a bearer token (1h TTL; reuse until expiry).
2. **Find the payment** — `listpayments` (cursor-paginated, filterable) to locate the payment, or `getpayment` by ID if you already have it.
3. **Refund** — `refundaction`: issue a full or partial refund against the captured payment (amount + currency; amount omitted or equal to captured = full refund).

## Rules
- **Idempotency**: always send an idempotency key so a retried refund is not applied twice.
- **State**: `OperationNotAllowed` means the payment state does not permit a refund (e.g. not yet captured); `DuplicateOperation` means the refund was already applied.
- **Confirmation is async**: the refund settles via provider callback — watch for the `executionActionUpdated` webhook rather than assuming success from the sync response.
- **Errors**: `request.conflict`/`workflow.action.not-allowed` on the `errors[]` envelope indicate the action is not valid for the current workflow state.
