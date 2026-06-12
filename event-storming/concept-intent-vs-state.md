# Events as Intent, Not State

> **Each event captures a distinct intention — not just a state transition.**

---

## The CRUD instinct

```
Order.status = "CONFIRMED"
```

One row update. Happy path is clean. But the *how* and *why* are lost.

## The event stream

```
OrderPlaced → PaymentAuthorized → OrderConfirmed
```

Three events. Looks redundant on the happy path. It isn't.

## Why segregation matters

| If you collapse... | You lose the ability to ask... |
|---|---|
| `PaymentAuthorized` into `OrderConfirmed` | "How many orders failed authorization?" |
| `OrderPicked` into `OrderShipped` | "What's our warehouse pick speed?" |
| `PaymentCaptured` into `PaymentAuthorized` | "When did money actually move vs. get reserved?" |

Each separate event is a **fact** that someone downstream will query — accounting, fraud, warehouse, customer support. The granularity isn't ceremony. It's the record of truth.

## Mindset shift

| CRUD | Event Sourcing |
|------|---------------|
| "What's the current state?" | "What happened?" |
| Optimistic: assume success | Realistic: failures are part of the story |
| Collapse for simplicity | Segregate for traceability |
| One consumer (the UI) | Many consumers (fraud, analytics, warehouse, support...) |
