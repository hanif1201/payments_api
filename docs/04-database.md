# Database Design

Version: 1.0

---

# Overview

The Payments API uses PostgreSQL as its primary relational database. The schema is designed to ensure transactional integrity, auditability, and scalability.

---

# Design Goals

- Ensure ACID-compliant transactions.
- Maintain accurate wallet balances.
- Record every financial operation.
- Prevent duplicate transactions.
- Support audit and reconciliation.

---

# Core Entities

## Users

Represents registered users of the platform.

| Field | Type | Description |
|--------|------|-------------|
| id | UUID | Primary Key |
| first_name | VARCHAR | User's first name |
| last_name | VARCHAR | User's last name |
| email | VARCHAR | Unique email address |
| password_hash | VARCHAR | Hashed password |
| created_at | TIMESTAMP | Creation date |
| updated_at | TIMESTAMP | Last update |

---

## Wallets

Represents a user's wallet.

| Field | Type | Description |
|--------|------|-------------|
| id | UUID | Primary Key |
| user_id | UUID | Owner of wallet |
| balance | DECIMAL | Current balance |
| currency | VARCHAR | Wallet currency |
| status | VARCHAR | Active, Frozen, Closed |
| created_at | TIMESTAMP | Creation date |

---

## Transactions

Represents every financial transaction.

| Field | Type | Description |
|--------|------|-------------|
| id | UUID | Primary Key |
| wallet_id | UUID | Wallet involved |
| reference | VARCHAR | Unique transaction reference |
| type | VARCHAR | Deposit, Withdrawal, Transfer |
| amount | DECIMAL | Transaction amount |
| status | VARCHAR | Pending, Successful, Failed |
| created_at | TIMESTAMP | Creation date |

---

## Ledger Entries

Stores accounting records for every transaction.

| Field | Type | Description |
|--------|------|-------------|
| id | UUID | Primary Key |
| transaction_id | UUID | Related transaction |
| entry_type | VARCHAR | Debit or Credit |
| account | VARCHAR | Ledger account |
| amount | DECIMAL | Entry amount |
| created_at | TIMESTAMP | Creation date |

---

## Webhook Events

Stores incoming webhook notifications.

| Field | Type | Description |
|--------|------|-------------|
| id | UUID | Primary Key |
| provider | VARCHAR | Payment provider |
| event_type | VARCHAR | Event name |
| payload | JSONB | Raw webhook payload |
| processed | BOOLEAN | Processing status |
| created_at | TIMESTAMP | Creation date |

---

## Idempotency Keys

Prevents duplicate request processing.

| Field | Type | Description |
|--------|------|-------------|
| id | UUID | Primary Key |
| key | VARCHAR | Idempotency key |
| request_hash | VARCHAR | Request fingerprint |
| response | JSONB | Cached response |
| status | VARCHAR | Processing status |
| created_at | TIMESTAMP | Creation date |

---

# Entity Relationships

- One User has one Wallet.
- One Wallet has many Transactions.
- One Transaction has many Ledger Entries.
- Webhook Events may update Transactions.
- Idempotency Keys are associated with client requests.

---

# Database Constraints

- Email must be unique.
- Transaction reference must be unique.
- Wallet balance cannot be negative.
- Foreign keys enforce referential integrity.
- Financial operations must execute within database transactions.

---

# Indexing Strategy

Create indexes for:

- email
- wallet_id
- reference
- created_at
- status
- provider

---

# Future Tables

The following tables may be introduced in future versions:

- merchants
- bank_accounts
- payment_methods
- settlements
- reconciliation_reports
- fraud_cases
- audit_logs