### Q4(a)

**Feature 1: Digital Ordering & Payment Checkout**
* **User Stories Implemented:** Diner - Browse menu & place order remotely; Diner - Pay via PayNow/Credit Card
* **Permitted Personas:** Diner.
* **Expected Behaviour:** The system allows a diner to browse a vendor's menu, add items to a cart, calculate the total, and complete the transaction via a digital payment gateway.
* **Key Data:** Order (OrderID, DinerID, VendorID, TotalAmount, OrderStatus); Payment (PaymentID, OrderID, PaymentStatus).
* **Rules & Validation:** A cart can only contain items from a single vendor at one time. Checkout is blocked if the cart total is $0.00.
* **Workflow:** Cart Assembly → Checkout Initiated → Payment Gateway Processing → Payment 'Success' Received → OrderStatus set to 'New/Pending'.

**Feature 2: Order Fulfillment & Collection Management**
* **User Stories Implemented:** Vendor - Mark order as “Ready for collection”
* **Permitted Personas:** Food Vendor (Action), Diner (View).
* **Expected Behaviour:** The vendor dashboard displays an active queue. The vendor can advance the order state to notify the diner that food is ready for pickup.
* **Key Data:** Order (OrderID, OrderStatus).
* **Rules & Validation:** Status transitions strictly enforce: Pending → Preparing → Ready → Collected. Orders marked Collected or Cancelled cannot be reverted.
* **Workflow:** Vendor taps 'Mark as Ready' → OrderStatus updates to 'Ready' → System pushes a UI notification to Diner → Order moves to 'Collection' queue.

**Feature 3: Real-Time Menu Inventory Toggle**
* **User Stories Implemented:** Vendor - Toggle menu items to "Sold Out".
* **Permitted Personas:** Food Vendor. 
* **Expected Behaviour:** Vendors can instantly switch the availability status of menu items to block new orders for out-of-stock food.
* **Key Data:** MenuItem (MenuItemID, VendorID, IsAvailable boolean).
* **Rules & Validation:** When IsAvailable is set to False, the item is greyed out on the diner menu, disabling the "Add to Cart" action.

**Feature 4: Vendor Sales Dashboard**
* **User Stories Implemented:** Vendor - View sales records & best-selling items
* **Permitted Personas:** Food Vendor.
* **Expected Behaviour:** The system aggregates completed orders to display daily total revenue and item-level sales volumes.
* **Key Data:** Read-only aggregations of Order and OrderItem.
* **Rules & Validation:** Only orders with a PaymentStatus of 'Success' are included in the revenue calculations.

### Q4(b)

* **Usability & Accessibility:** The diner ordering interface must achieve a 90% order-completion rate for users aged 55+ on their first attempt without external assistance. Based on the survey revealed that diners aged 55+ have the lowest willingness to adopt the app (mean: 3.0). This shows that complex UI will result lesser users from this demographic.
* **Performance:** The system must process payment gateway responses and generate the finalised OrderID in under 3 seconds. The survey highlights extreme diner frustration with waiting (queue pain mean: 4.2). This means that slow digital checkout rendering would replicate the bottleneck of a physical queue, defeating the app's core purpose.
* **Reliability:** The vendor dashboard must poll and reflect new paid orders in under 5 seconds of payment completion. Since vendors currently face severe peak-hour stress leading to a 100% order error rate. Delayed order syncing will cause missed preparations and exacerbate counter arguments.

### Q4(c)

* **Split-Bill Payments:** Building a split-bill feature is too technically complex for a Version 1 launch. Diners can just transfer funds privately via PayNow.
* **Diner Order History:** Saving past orders for quick reordering is a retention feature that does not contribute to the essential baseline of processing a live food court transaction.