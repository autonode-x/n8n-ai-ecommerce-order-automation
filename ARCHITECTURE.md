# E-commerce Order Intake & Confirmation — Architecture

![Architecture infographic](assets/architecture.png)


**Accept an order request, validate it, detect common duplicates, save a minimal record, and email a receipt-of-request confirmation.**

## 1. Workflow at a glance

```mermaid
flowchart LR
    A["1. ORDER REQUEST<br/>Order ID, customer email,<br/>product, quantity, price"] --> B["2. VALIDATE<br/>Types, formats, bounds"]
    B -->|invalid| X["HTTP 400<br/>Invalid request"]
    B -->|valid| C["3. DUPLICATE LOOKUP<br/>Order ID in Sheets"]
    C --> D{"Existing order?"}
    D -->|yes| E["DUPLICATE RESPONSE<br/>No repeat confirmation"]
    D -->|no| F["4. SAVE ORDER<br/>ID, email, product, quantity"]
    F --> G{"Saved?"}
    G -->|no| H["HTTP 500<br/>Persistence alert"]
    G -->|yes| I["5. EMAIL RECEIPT<br/>Submitted customer email"]
    I --> J{"Email succeeded?"}
    J -->|yes| K["6. RECORD SENT<br/>HTTP 201 + Slack"]
    J -->|no| L["MANUAL CHECK<br/>Update status + alert"]
    classDef input fill:#f3f1ff,stroke:#7256bd,color:#16112b,stroke-width:1.5px
    classDef validate fill:#eef5ff,stroke:#4b79c7,color:#102343,stroke-width:1.5px
    classDef action fill:#fff5e8,stroke:#d98a36,color:#4e2c0c,stroke-width:1.5px
    classDef output fill:#eff9f6,stroke:#238a77,color:#123a32,stroke-width:1.5px
    classDef error fill:#fff0f2,stroke:#cb5367,color:#571d29,stroke-width:1.5px
    class A input
    class B,C,D,G,J validate
    class F,I action
    class E,K output
    class X,H,L error
```

## 2. Input and output boundary

| Input | Workflow output |
|---|---|
| Order ID, customer email, product name, quantity, caller-supplied price | Minimal Sheet record, order-total receipt email, status updates, Slack operational alerts |

The email explicitly confirms receipt of a request only. Payment, inventory reservation, shipment, and fulfillment are not connected. The visible new-order mapping does not save the price/total, although the email computes a total from the submitted price.

## 3. Services used

`n8n` · `Google Sheets` order log · `Gmail` customer receipt · `Slack` operations notices.

## 4. Security and integrity notes

- Source webhook is unauthenticated. Use a signed/authenticated commerce-platform caller; do not expose as a public email relay.
- Resolve price from a trusted server-side catalogue; caller-supplied price is not authoritative.
- Verify customer/order identity before sending to the submitted address. Validate order state in the source commerce system.
- Sheet lookup-then-upsert can race. Use an atomic idempotency mechanism for strict duplicate prevention.
- Protect customer email/order data and use synthetic records in portfolio demos.

> **Portfolio output:** 21 nodes and 14 connection groups. This is an intake/confirmation prototype, not a payment or fulfillment system.
