# Phase 1 — Big Picture: Domain Events

> **Rule:** Every event is in **past tense** — something that *already occurred*.  
> **Goal:** Lay out the full B2C e-commerce customer journey chronologically.  
> **Color:** Orange sticky notes.

---

## Browsing & Discovery

- **ProductSearchPerformed** — A customer searched with a keyword/filters and received results.
- **ProductViewed** — A customer opened a specific product's detail page.
- **ProductLiked** — A customer liked/favorited a product (social/algorithmic signal).

## Cart Management

- **ProductAddedToCart** — A customer added a product (with quantity) to their cart.
- **ProductRemovedFromCart** — A customer removed an item from their cart.
- **CartItemQuantityChanged** — A customer adjusted the quantity of an item in their cart.
- **CartAbandoned** — A customer left their cart inactive without checking out (after a defined time window).

## Checkout & Payment

- **CheckoutInitiated** — A customer moved from their cart into the checkout flow.
- **ShippingAddressProvided** — A customer provided or selected a shipping destination for the order.
- **PaymentMethodSelected** — A customer chose how they want to pay (card, PayPal, etc.).
- **OrderReviewed** — A customer reviewed the final order summary before confirming.
- **OrderPlaced** — A customer confirmed the order; the order now exists as a binding commitment.
- **PaymentAuthorized** — The payment gateway confirmed funds are available and reserved.
- **PaymentCaptured** — The payment was actually transferred (often at shipment time, not at order time).
- **OrderConfirmed** — All checks passed; the order is accepted and ready for fulfillment.

## Fulfillment

- **FulfillmentRequested** — The confirmed order was handed over to the warehouse to prepare for shipping.
- **InventoryReserved** — Stock was set aside for this order; no other order can claim those units.
- **OrderPicked** — A warehouse worker physically collected the items from the shelves.
- **OrderPacked** — Items were boxed and labeled with shipping info.
- **OrderShipped** — The package was handed to the shipping carrier; tracking number assigned.
- **OrderDelivered** — The carrier confirmed successful delivery to the customer's address.

## Post-Purchase

- **ReturnRequested** — A customer requested to return an item (within or outside the return window).
- **ReturnApproved** — The return was accepted; meets policy criteria (window, condition, etc.).
- **ReturnRejected** — The return was denied (outside policy window, item condition, non-returnable).
- **ReturnReceived** — The warehouse confirmed receipt of the returned item.
- **RefundIssued** — Money was sent back to the customer's original payment method.

---

## Event Inventory

| # | Domain Event | Category | Description |
|---|---|---|---|
| 1 | ProductSearchPerformed | Browsing | Customer searched with keyword/filters and received results |
| 2 | ProductViewed | Browsing | Customer opened a specific product's detail page |
| 3 | ProductLiked | Browsing | Customer liked/favorited a product (social/algorithmic signal) |
| 4 | ProductAddedToCart | Cart | Customer added a product with quantity to their cart |
| 5 | ProductRemovedFromCart | Cart | Customer removed an item from their cart |
| 6 | CartItemQuantityChanged | Cart | Customer adjusted the quantity of a cart item |
| 7 | CartAbandoned | Cart | Customer left cart inactive without checking out |
| 8 | CheckoutInitiated | Checkout | Customer moved from cart into the checkout flow |
| 9 | ShippingAddressProvided | Checkout | Customer provided/selected a shipping destination |
| 10 | PaymentMethodSelected | Checkout | Customer chose their payment method |
| 11 | OrderReviewed | Checkout | Customer reviewed the final order summary before confirming |
| 12 | OrderPlaced | Checkout | Customer confirmed; order now exists as binding commitment |
| 13 | PaymentAuthorized | Payment | Payment gateway confirmed funds are available and reserved |
| 14 | PaymentCaptured | Payment | Payment was actually transferred |
| 15 | OrderConfirmed | Checkout | All checks passed; order accepted for fulfillment |
| 16 | FulfillmentRequested | Fulfillment | Order was handed over to the warehouse |
| 17 | InventoryReserved | Fulfillment | Stock was set aside for this order |
| 18 | OrderPicked | Fulfillment | Warehouse worker collected items from shelves |
| 19 | OrderPacked | Fulfillment | Items were boxed and labeled |
| 20 | OrderShipped | Fulfillment | Package handed to carrier; tracking number assigned |
| 21 | OrderDelivered | Fulfillment | Carrier confirmed successful delivery |
| 22 | ReturnRequested | Post-Purchase | Customer requested to return an item |
| 23 | ReturnApproved | Post-Purchase | Return was accepted; meets policy criteria |
| 24 | ReturnRejected | Post-Purchase | Return was denied (policy, condition, etc.) |
| 25 | ReturnReceived | Post-Purchase | Warehouse confirmed receipt of returned item |
| 26 | RefundIssued | Post-Purchase | Money was sent back to customer's payment method |

---

## Diagram

Open `event-storming/diagrams/big-picture-events.excalidraw` in [Excalidraw](https://excalidraw.com) (drag & drop) or with the VS Code Excalidraw extension.

---

## Next: Phase 2 — Process Modeling
Add commands, actors, read models, policies, and external systems around each event cluster.
