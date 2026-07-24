# Business Rules

## Authentication

- Every email must be unique.
- Passwords must be hashed.
- JWT expires after 15 minutes.
- Refresh tokens expire after 7 days.

## Wallet

- Every user has exactly one wallet.
- Wallet currency cannot be changed after creation.
- Wallet balance cannot be negative.

## Deposits

- Deposits are initiated through a payment provider.
- Wallets are credited only after verified payment confirmation.

## Withdrawals

- Users cannot withdraw more than their available balance.
- Every withdrawal requires a unique transaction reference.

## Transfers

- Sender and receiver must both have active wallets.
- Transfers are atomic: either both wallets are updated or neither is.
- Every transfer creates matching ledger entries.