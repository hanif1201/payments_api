# System Flows

Version: 1.0

---

# Overview

This document describes the sequence of operations for the core business processes of the Payments API.

---

# User Registration

```mermaid
sequenceDiagram
    participant Client
    participant API
    participant Database

    Client->>API: Register User
    API->>API: Validate Request
    API->>Database: Create User
    API->>Database: Create Wallet
    Database-->>API: Success
    API-->>Client: Account Created
```

---

# User Login

```mermaid
sequenceDiagram
    participant Client
    participant API
    participant Database

    Client->>API: Login
    API->>Database: Find User
    Database-->>API: User Found
    API->>API: Verify Password
    API->>API: Generate JWT
    API-->>Client: Access Token
```

---

# Deposit Flow

```mermaid
sequenceDiagram
    participant Client
    participant API
    participant PaymentProvider
    participant Webhook
    participant Database

    Client->>API: Deposit Request
    API->>PaymentProvider: Initialize Payment
    PaymentProvider-->>Client: Payment Page

    Client->>PaymentProvider: Complete Payment

    PaymentProvider->>Webhook: Payment Successful

    Webhook->>Database: Verify Transaction
    Webhook->>Database: Credit Wallet
    Webhook->>Database: Create Ledger Entry
    Webhook->>Database: Update Transaction Status

    Webhook-->>Client: Payment Successful
```

---

# Withdrawal Flow

```mermaid
sequenceDiagram
    participant Client
    participant API
    participant Database
    participant PaymentProvider

    Client->>API: Withdrawal Request

    API->>Database: Verify Wallet Balance

    Database-->>API: Balance Available

    API->>PaymentProvider: Initiate Transfer

    PaymentProvider-->>API: Transfer Successful

    API->>Database: Debit Wallet
    API->>Database: Create Ledger Entry
    API->>Database: Save Transaction

    API-->>Client: Withdrawal Successful
```

---

# Wallet Transfer

```mermaid
sequenceDiagram
    participant Sender
    participant API
    participant Database
    participant Receiver

    Sender->>API: Transfer Funds

    API->>Database: Verify Balance

    API->>Database: Begin Transaction

    API->>Database: Debit Sender

    API->>Database: Credit Receiver

    API->>Database: Create Ledger Entries

    API->>Database: Commit Transaction

    API-->>Sender: Transfer Successful

    Receiver-->>Receiver: Wallet Credited
```

---

# Webhook Processing

```mermaid
sequenceDiagram
    participant PaymentProvider
    participant Webhook
    participant Database

    PaymentProvider->>Webhook: Event

    Webhook->>Webhook: Verify Signature

    Webhook->>Database: Check Idempotency

    Database-->>Webhook: Valid Request

    Webhook->>Database: Update Transaction

    Webhook->>Database: Create Ledger Entry

    Webhook->>Database: Credit Wallet

    Webhook-->>PaymentProvider: 200 OK
```

---

# Error Handling

## Failed Payment

- Transaction status becomes `FAILED`.
- Wallet balance remains unchanged.
- Ledger entries are not created.

---

## Duplicate Webhook

- Idempotency key is verified.
- Duplicate event is ignored.
- Existing response is returned.

---

## Insufficient Balance

- Withdrawal or transfer is rejected.
- Transaction status becomes `FAILED`.
- Wallet balance remains unchanged.