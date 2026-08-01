---
name: Tokenize and vault a card
description: Store card data in the Payrails PCI-certified vault and create a reusable token/instrument.
api: payrails
operations: [getoauthtoken, vaultpublicinfo, createinstrument, createtoken]
---

# Tokenize and vault a card (Payrails)

Keep raw PAN out of your servers — collect it client-side (Secure Fields) or encrypt it with the vault's public key.

## Steps
1. **Authenticate** — `getoauthtoken` for a bearer token.
2. **Get the encryption key** — `vaultpublicinfo`: retrieve the public encryption key and the `kid` value to use as the JWE `kid` field for client-side encryption.
3. **Create the instrument** — `createinstrument`: register the payment instrument for a holder.
4. **Create the token** — `createtoken`: tokenize the instrument's sensitive data into the PCI-certified vault; use the returned token/alias for future charges.

## Rules
- **PCI scope**: prefer Secure Fields / client-side encryption so raw card data never touches your backend.
- **Idempotency**: send an idempotency key on create calls to avoid duplicate instruments/tokens.
- **Result codes**: `InvalidInstrument`/`ExpiredInstrument` on `reason.result` indicate the card data was rejected (see `errors/payrails-decline-codes.yml`).
- **Network tokens**: for network tokenization use `provisionnetworktokenpublic` then `generatecryptogram` at charge time.
