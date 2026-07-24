# Entity Relationship Diagram (ERD)

Version: 1.0

---

## Overview

This diagram represents the core database entities and relationships for the Payments API.

```mermaid
erDiagram

    USERS ||--|| WALLETS : owns
    WALLETS ||--o{ TRANSACTIONS : records
    TRANSACTIONS ||--o{ LEDGER_ENTRIES : creates

    USERS {
        uuid id PK
        string first_name
        string last_name
        string email
        string password_hash
        datetime created_at
        datetime updated_at
    }

    WALLETS {
        uuid id PK
        uuid user_id FK
        decimal balance
        string currency
        string status
        datetime created_at
    }

    TRANSACTIONS {
        uuid id PK
        uuid wallet_id FK
        string reference
        string type
        decimal amount
        string status
        datetime created_at
    }

    LEDGER_ENTRIES {
        uuid id PK
        uuid transaction_id FK
        string entry_type
        string account
        decimal amount
        datetime created_at
    }

    WEBHOOK_EVENTS {
        uuid id PK
        string provider
        string event_type
        json payload
        boolean processed
        datetime created_at
    }

    IDEMPOTENCY_KEYS {
        uuid id PK
        string key
        string request_hash
        json response
        string status
        datetime created_at
    }
```

---

## Relationship Summary

- One User owns one Wallet.
- One Wallet can have many Transactions.
- One Transaction creates one or more Ledger Entries.
- Webhook Events update transaction status.
- Idempotency Keys prevent duplicate request processing.