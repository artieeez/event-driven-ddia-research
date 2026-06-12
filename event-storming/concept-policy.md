# Policy (Event Storming)

> **"When X happens, then do Y" — automated, no human in the loop.**

A Policy is a purple sticky note representing an **automated reaction** to a domain event.

---

## Signature

```
When <Event> → <Command>
```

## Three flavors

| Flavor | Triggers | Example |
|--------|----------|---------|
| **Reactive consumer** | Immediately after a single event | `When OrderPlaced → ReserveInventory` |
| **Saga / Process Manager** | When multiple events meet a condition | `When PaymentAuthorized AND InventoryReserved → ConfirmOrder` |
| **Scheduled / Timer** | Time-based, checks a read model | `Hourly: find carts idle 24h → AbandonCart` |

## What a Policy is NOT

- **NOT** a business rule (e.g., "max 5 items per order") — that's an invariant, lives inside an aggregate.
- **NOT** a human decision — if a person is in the loop, it's a command issued by an actor.
- **NOT** an external system — the policy *lives in your domain*. It may call an external system via the command it issues.

## Actor column

When a command is issued by a Policy (not a human), the **Actor** column in the process table names the policy itself — e.g., `CartAbandonmentPolicy`.
