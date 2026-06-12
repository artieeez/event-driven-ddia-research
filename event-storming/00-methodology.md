# Phase 0 — Event Storming Methodology

## What is Event Storming?

Event Storming is a collaborative workshop technique created by Alberto Brandolini. It uses sticky notes on a wide wall to model business processes domain experts and developers together — focusing on **what happens** in the business, not how it's implemented.

---

## The Legend (Sticky Note Colors)

| Color | Meaning | Description | Example |
|---|---|---|---|
| **Orange** | Domain Event | Something that *already happened* — always past tense | `OrderPlaced`, `PaymentCaptured` |
| **Blue** | Command | A *request* to do something — the trigger for an event | `PlaceOrder`, `CapturePayment` |
| **Small Yellow** | Actor | A *person* (role) issuing the command | `Customer`, `Warehouse Worker` |
| **Green** | Read Model | Information the actor *looks at* before deciding | `CartView`, `OrderSummaryView` |
| **Purple/Lilac** | Policy | An *automated* reaction: "when X happens, do Y" | `When OrderPlaced → ReserveInventory` |
| **Pink** | External System | An integration outside our control | `PaymentGateway`, `ShippingCarrier` |
| **Hot Pink (rotated)** | Hotspot | Unknowns, conflicts, decisions to make | "Who pays for return shipping?" |

---

## The Four Phases

1. **Big Picture** (Phase 1)
   - Paste orange Domain Events across the wall in chronological order
   - Focus on the narrative: "First this happens, then that happens..."
   - No actors, no structure — just the timeline

2. **Process Modeling** (Phase 2)
   - Add Commands (blue) before each event
   - Add Actors (small yellow) beside each command
   - Add Read Models (green) that inform decisions
   - Add Policies (purple) for automated reactions
   - Add External Systems (pink) for integrations
   - Mark Hotspots (hot pink) for unknowns

3. **Design Level** (Phase 3)
   - Extract Aggregates from event/command clusters
   - Define transaction boundaries and invariants
   - Identify Aggregate roots

4. **Bounded Contexts** (Phase 4)
   - Group aggregates by Ubiquitous Language
   - Draw context relationships (Customer/Supplier, ACL, etc.)
   - Produce the bounded context map

---

## Domain: B2C E-Commerce

**Scope:** Browsing & discovery → cart → checkout → payment → order fulfillment → post-purchase (returns)

**Bounded Contexts (initial hypothesis):**
- **Shopping:** Browse products, manage cart
- **Ordering:** Checkout, place orders, payment
- **Fulfillment:** Pick, pack, ship orders
- **Returns:** Handle returns and refunds

These hypotheses will be refined through the workshop.
