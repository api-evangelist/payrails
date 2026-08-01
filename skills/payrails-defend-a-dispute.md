---
name: Defend a dispute
description: Retrieve a Payrails dispute, upload evidence, and submit a defense/representment.
api: payrails
operations: [getoauthtoken, listdisputes, getdispute, uploaddisputeevidence, defenddispute, submitdisputeevidence]
---

# Defend a dispute (Payrails)

## Steps
1. **Authenticate** — `getoauthtoken` for a bearer token.
2. **Find the dispute** — `listdisputes` (multi-criteria filtering) or `getdispute` by ID (expandable object) to inspect status and deadline.
3. **Upload evidence** — `uploaddisputeevidence`: upload the evidence files (receipts, delivery proof, etc.).
4. **Defend** — `defenddispute`: submit the defense material to the payment provider, referencing the uploaded evidence.
5. **Submit / finalize** — `submitdisputeevidence`: finalize and submit the evidence package.

## Rules
- **Deadlines**: disputes are time-boxed — check the dispute object before starting; missing the window forfeits the case.
- **Accept vs defend**: use `acceptdispute` to concede rather than defend.
- **Representment**: for card representments, `generatedisputerepresentmentplan` → `submitdisputerepresentmentplanevidences` → `generatedisputerepresentmentpdf`.
- **Notifications**: dispute lifecycle changes arrive via dispute webhooks (verify `X-Signature`).
