# Phase 2 — Process Modeling

> **Goal:** For each Domain Event cluster from Phase 1, add the surrounding context:
> - **Commands** (blue) — What request triggered the event?
> - **Actors** (small yellow) — Who issued the command?
> - **Read Models** (green) — What information was consulted to make the decision?
> - **Policies** (purple/lilac) — What automated reaction fires after an event?
> - **External Systems** (pink) — What integrations are involved?
> - **Hotspots** (hot pink rotated) — What do we still not understand?

---

## Browsing & Discovery

| Event | Command | Actor | Read Model | External System |
|-------|---------|-------|------------|-----------------|
| ProductSearchPerformed | SearchProducts | Customer | SearchBar | SearchEngine |
| ProductViewed | ViewProduct | Customer | SearchResultsView | — |
| ProductLiked | LikeProduct | Customer | ProductDetailView | — |

---

## Cart Management

| Event | Command | Actor | Read Model | External System |
|-------|---------|-------|------------|-----------------|
| ProductAddedToCart | AddProductToCart | Customer | ProductDetailView | — |
| ProductRemovedFromCart | RemoveProductFromCart | Customer | CartView | — |
| CartItemQuantityChanged | ChangeCartItemQuantity | Customer | CartView | — |
| CartAbandoned | AbandonCart | CartAbandonmentPolicy | CartActivityView | — |

---

## Checkout & Payment

| Event | Command | Actor | Read Model | External System |
|-------|---------|-------|------------|-----------------|
| CheckoutInitiated | InitiateCheckout | Customer | CartView | — |
| ShippingAddressProvided | ProvideShippingAddress | Customer | CheckoutFormView | — |
| PaymentMethodSelected | SelectPaymentMethod | Customer | CheckoutFormView | — |
| OrderReviewed | ReviewOrder | Customer | OrderSummaryView | — |
| OrderPlaced | PlaceOrder | Customer | OrderSummaryView | — |
| PaymentAuthorized | AuthorizePayment | PaymentAuthorizationPolicy | OrderView | PaymentGateway |
| PaymentCaptured | CapturePayment | PaymentCapturePolicy | PaymentView | PaymentGateway |
| OrderConfirmed | ConfirmOrder | OrderConfirmationPolicy | OrderView | — |

---

## Fulfillment

| Event | Command | Actor | Read Model | External System |
|-------|---------|-------|------------|-----------------|
| FulfillmentRequested | RequestFulfillment | FulfillmentTriggerPolicy | OrderView | — |
| InventoryReserved | ReserveInventory | InventoryReservationPolicy | InventoryView | — |
| OrderPicked | PickOrder | WarehouseWorker | PickListView | — |
| OrderPacked | PackOrder | WarehouseWorker | PackingListView | — |
| OrderShipped | ShipOrder | WarehouseWorker | PackedOrderView | ShippingCarrier |
| OrderDelivered | ConfirmDelivery | ShippingCarrier | ShipmentTrackingView | ShippingCarrier |

---

## Post-Purchase

| Event | Command | Actor | Read Model | External System |
|-------|---------|-------|------------|-----------------|
| ReturnRequested | RequestReturn | Customer | OrderHistoryView | — |
| ReturnApproved | ApproveReturn | ReturnEvaluationPolicy | ReturnPolicyView | — |
| ReturnRejected | RejectReturn | ReturnEvaluationPolicy | ReturnPolicyView | — |
| ReturnReceived | ReceiveReturn | WarehouseWorker | ReturnListView | — |
| RefundIssued | IssueRefund | RefundIssuancePolicy | ReturnView | PaymentGateway |

---

## Policies (automated reactions)

| When... | Then... | Description |
|---------|---------|-------------|
| Cart inactive for 24h | AbandonCart | Marks stale cart as abandoned |
| OrderPlaced | AuthorizePayment | Attempts to authorize funds via payment gateway |
| PaymentAuthorized | ConfirmOrder | All checks passed; order now ready for fulfillment |
| OrderShipped | CapturePayment | Captures reserved funds once goods leave warehouse |
| OrderConfirmed | RequestFulfillment | Hands order over to warehouse for picking |
| FulfillmentRequested | ReserveInventory | Sets stock aside for this order |
| ReturnRequested | EvaluateReturn | Checks return window, item condition, against policy rules |
| ReturnApproved AND ReturnReceived | IssueRefund | Releases money back to customer's payment method |

---

## Hotspots (unresolved)

| # | Question | Context |
|---|----------|---------|
| | | |

---

## Diagram

Open `event-storming/diagrams/process-modeling.excalidraw` in [Excalidraw](https://excalidraw.com) (drag & drop) or with the VS Code Excalidraw extension.

Each event card shows: **EventName** on line 1, **Command · Actor** on line 2. Policy-driven actors are marked with `[Policy]`. Green pills on the right = Read Models. Pink pills = External Systems. Arrows show sequential flow within each section.

---

## Next: Phase 3 — Aggregates
Extract aggregates, define invariants and transaction boundaries, identify aggregate roots from the command/event clusters.
