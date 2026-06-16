# Phase 3 — Design Level: Aggregates

> **Goal:** Extract transactional consistency boundaries from the command/event clusters of Phase 2.
> For each aggregate: define the root entity, invariants, handled commands, and emitted events.
>
> **Key rule:** An aggregate is the unit of consistency — all invariants within it must hold after
> every transaction. Changes to different aggregates can happen in separate transactions.

---

## Events Without Aggregates

Some Phase 2 events are informational/advisory — no business invariant to protect. Their command
handlers emit events directly without loading an aggregate.

| Event | Reason |
|-------|--------|
| `ProductSearchPerformed` | Telemetry signal; no state to protect |
| `ProductViewed` | Telemetry signal; no state to protect |
| `ProductLiked` | Accumulating counter signal; idempotent, no deduplication invariant |

---

## Aggregates Summary

| # | Aggregate | Root | Events Handled | Lifecycle |
|---|-----------|------|----------------|-----------|
| 1 | Cart | `Cart` | 4 | Active → Abandoned |
| 2 | Order | `Order` | 6 | Draft → Placed → Confirmed |
| 3 | Payment | `Payment` | 2 | Authorized → Captured |
| 4 | Inventory | `InventoryItem` | 1 | n/a (quantity-based) |
| 5 | Shipment | `Shipment` | 5 | Requested → Picked → Packed → Shipped → Delivered |
| 6 | Return | `Return` | 5 | Requested → Approved → Received → Refunded (or Rejected) |

---

## 1. Cart Aggregate

**Root:** `Cart` (identified by `cartId`, associated with `customerId`)

### Invariants

1. Items can only be added if the cart is not abandoned and not checked out
2. Quantity changes must result in a positive quantity (> 0); use `RemoveItem` to reach zero
3. An item can only be removed if it exists in the cart
4. A cart can only be abandoned if not already abandoned or checked out

### Commands, Events & Guards

| Command | Event | Guard (invariant enforced) |
|---------|-------|---------------------------|
| `AddProductToCart` | `ProductAddedToCart` | Cart not abandoned, not checked out |
| `RemoveProductFromCart` | `ProductRemovedFromCart` | Item exists in cart |
| `ChangeCartItemQuantity` | `CartItemQuantityChanged` | Cart not abandoned, not checked out; quantity > 0 |
| `AbandonCart` | `CartAbandoned` | Cart not already abandoned, not checked out |

### Lifecycle

```
(Available) ──[AddProductToCart]──→ Active ──[AbandonCart]──→ Abandoned
                                        │
                                        └──[InitiateCheckout]──→ (transitions to Order)
```

---

## 2. Order Aggregate

**Root:** `Order` (identified by `orderId`, associated with `customerId`)

### Invariants

1. Checkout can only be initiated from a non-empty, non-abandoned cart
2. Shipping address and payment method can only be set while in Draft state
3. `ReviewOrder` requires shipping address + payment method to both be set
4. `PlaceOrder` requires all required checkout fields present, order must be in Draft state
5. `ConfirmOrder` requires the order to be in Placed state and payment to be authorized

### Commands, Events & Guards

| Command | Event | Guard |
|---------|-------|-------|
| `InitiateCheckout` | `CheckoutInitiated` | Cart exists, not empty, not abandoned |
| `ProvideShippingAddress` | `ShippingAddressProvided` | Order in Draft state |
| `SelectPaymentMethod` | `PaymentMethodSelected` | Order in Draft state |
| `ReviewOrder` | `OrderReviewed` | Shipping address + payment method set |
| `PlaceOrder` | `OrderPlaced` | Order in Draft; all required fields present |
| `ConfirmOrder` | `OrderConfirmed` | Order in Placed; payment authorized |

### Lifecycle

```
(Cart) ──[InitiateCheckout]──→ Draft ──[PlaceOrder]──→ Placed ──[ConfirmOrder]──→ Confirmed
```

### Relationships

- References `Payment` by `paymentId` (separate aggregate, eventual consistency)
- Created from `Cart` at checkout initiation

---

## 3. Payment Aggregate

**Root:** `Payment` (identified by `paymentId`, associated with `orderId`)

### Invariants

1. Authorization can only happen once per payment
2. Capture requires the payment to be in Authorized state
3. Capture should only occur after the associated shipment has shipped

### Commands, Events & Guards

| Command | Event | Guard |
|---------|-------|-------|
| `AuthorizePayment` | `PaymentAuthorized` | Payment not already authorized; order is Placed |
| `CapturePayment` | `PaymentCaptured` | Payment is Authorized; shipment is Shipped |

### Lifecycle

```
(OrderPlaced) ──[AuthorizePayment]──→ Authorized ──[CapturePayment]──→ Captured
```

### Relationships

- Linked to `Order` by `orderId` (separate aggregate)
- Integrates with `PaymentGateway` external system for actual fund operations

---

## 4. Inventory Aggregate

**Root:** `InventoryItem` (per product/SKU, identified by `productId`)

### Invariants

1. Can only reserve if `availableQuantity ≥ requestedQuantity`
2. Cannot double-reserve the same units for different orders

### Commands, Events & Guards

| Command | Event | Guard |
|---------|-------|-------|
| `ReserveInventory` | `InventoryReserved` | Available quantity ≥ requested amount |

### Lifecycle

No state machine — inventory is quantity-based. Reservation reduces available quantity.

---

## 5. Shipment Aggregate

**Root:** `Shipment` (identified by `shipmentId`, associated with `orderId`)

### Invariants

1. Fulfillment can only be requested for a Confirmed order
2. Picking requires the shipment to be in Requested state
3. Packing requires the shipment to be in Picked state
4. Shipping requires the shipment to be in Packed state and a tracking number
5. Delivery confirmation requires the shipment to be in Shipped state

### Commands, Events & Guards

| Command | Event | Guard |
|---------|-------|-------|
| `RequestFulfillment` | `FulfillmentRequested` | Order is Confirmed |
| `PickOrder` | `OrderPicked` | Shipment is Requested |
| `PackOrder` | `OrderPacked` | Shipment is Picked |
| `ShipOrder` | `OrderShipped` | Shipment is Packed; tracking number provided |
| `ConfirmDelivery` | `OrderDelivered` | Shipment is Shipped |

### Lifecycle

```
Requested ──[PickOrder]──→ Picked ──[PackOrder]──→ Packed ──[ShipOrder]──→ Shipped ──[ConfirmDelivery]──→ Delivered
```

### Relationships

- Linked to `Order` by `orderId`
- Integrates with `ShippingCarrier` external system (tracking, delivery confirmation)

---

## 6. Return Aggregate

**Root:** `Return` (identified by `returnId`, associated with `orderId`)

### Invariants

1. Return can only be requested for a delivered order
2. The same items cannot have a duplicate active return request
3. Approval/rejection requires the return to be in Requested state
4. Receipt confirmation requires the return to be in Approved state
5. Refund issuance requires the return to be in Received state and not already refunded

### Commands, Events & Guards

| Command | Event | Guard |
|---------|-------|-------|
| `RequestReturn` | `ReturnRequested` | Order is Delivered; no duplicate active return for same items |
| `ApproveReturn` | `ReturnApproved` | Return is Requested; meets return policy criteria |
| `RejectReturn` | `ReturnRejected` | Return is Requested; fails return policy criteria |
| `ReceiveReturn` | `ReturnReceived` | Return is Approved |
| `IssueRefund` | `RefundIssued` | Return is Received; refund not already issued |

### Lifecycle

```
Requested ──[ApproveReturn]──→ Approved ──[ReceiveReturn]──→ Received ──[IssueRefund]──→ Refunded
     │
     └──[RejectReturn]──→ Rejected
```

### Relationships

- Linked to `Order` by `orderId`
- Refund execution flows through `Payment` aggregate
- Integrates with `PaymentGateway` external system for actual refund processing

---

## Aggregate Relationship Map

```
Cart ──(transitions into)──→ Order
                               │
                    ┌──────────┼──────────┐
                    ▼          ▼          ▼
                Payment    Shipment    Return
                    │          │          │
                    │          │          │
                    ▼          ▼          ▼
              PaymentGateway  ShippingCarrier  PaymentGateway
                              │
                    Inventory (reserved on FulfillmentRequested, via policy)
```

**Consistency model:** Each aggregate is its own transactional boundary. Cross-aggregate coordination
happens through domain events and policies (eventual consistency):

- `OrderPlaced` → `AuthorizePayment` (policy triggers Payment command)
- `PaymentAuthorized` → `ConfirmOrder` (policy triggers Order command)
- `OrderConfirmed` → `RequestFulfillment` (policy triggers Shipment command)
- `RequestFulfillment` → `ReserveInventory` (policy triggers Inventory command)
- `OrderShipped` → `CapturePayment` (policy triggers Payment command)
- `OrderDelivered` enables `RequestReturn` (guard on Return aggregate)
- `ReturnApproved` + `ReturnReceived` → `IssueRefund` (policy triggers Return command)

---

## Next: Phase 4 — Bounded Contexts

Group aggregates by ubiquitous language and cohesion into bounded contexts, then produce
the context map and relationship diagram.
