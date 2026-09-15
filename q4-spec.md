### Q4(a)

**Feature 1: Digital Ordering & Payment Checkout**
* **User Stories Implemented:** Diner - Browse menu & place order remotely; Diner - Pay via PayNow/Credit Card
* **Permitted Personas:** Diner.
* **Expected Behaviour:** The system allows a diner to browse a vendor's menu, add items to a cart, calculate the total, and complete the transaction via a digital payment gateway.
* **Key Data:** Order (OrderID, DinerID, VendorID, TotalAmount, OrderStatus); Payment (PaymentID, OrderID, PaymentStatus), OrderItem (OrderItemID, OrderID, MenuItemID, Quantity, UnitPrice).
* **Data Relationships:** Each Order records the diner and vendor associated with the transaction and contains the items being purchased. Each OrderItem records an item included in the order, together with its quantity and unit price. Each Payment records the payment status associated with the corresponding order. 
* **Rules & Validation:** A cart can only contain items from a single vendor at one time. Checkout is blocked if the cart total is $0.00.
* **Workflow:** Cart Assembly → Checkout Initiated → Payment Gateway Processing → Payment 'Success' Received → OrderStatus set to 'New/Pending'.

**Feature 2: Order Fulfillment & Collection Management**
* **User Stories Implemented:** Vendor - Mark order as “Ready for collection”
* **Permitted Personas:** Food Vendor (Action), Diner (View).
* **Expected Behaviour:** The vendor dashboard displays an active queue. The vendor can advance the order state to notify the diner that food is ready for pickup.
* **Key Data:** Order (OrderID, QueueNumber, OrderStatus, UpdatedAt).
* **Data Relationships:** OrderStatus records the current fulfilment stage of Order, while UpdatedAt records the latest time the order status was changed. QueueNumber identifies the order within the vendor's fulfilment queue.  
* **Rules & Validation:** Status transitions strictly enforce: Pending → Preparing → Ready → Collected. Orders marked Collected or Cancelled cannot be reverted.
* **Workflow:** Vendor taps 'Mark as Ready' → OrderStatus updates to 'Ready' → System pushes a UI notification to Diner → Order moves to 'Collection' queue.

**Feature 3: Real-Time Menu Inventory Toggle**
* **User Stories Implemented:** Vendor - Toggle menu items to "Sold Out".
* **Permitted Personas:** Food Vendor. 
* **Expected Behaviour:** Vendors can instantly switch the availability status of menu items between "Available" and "Sold Out" to reflect real-time inventory.
* **Key Data:** MenuItem (MenuItemID, VendorID, ItemName, IsAvailable).
* **Data Relationships:** Each MenuItem belongs to a vendor. The IsAvailable value determines whether the menu item can be added to a diner's cart. 
* **Rules & Validation:** When IsAvailable is set to False, the item is greyed out on the diner menu, disabling the "Add to Cart" action.
* **Workflow:** Vendor toggles item switch (Available<-->Sold Out) --> System updates IsAvailable status in DB --> Real-time interface update reflects immediately on active Diner menus (item grayed out / enabled for ordering).

**Feature 4: Vendor Sales Dashboard**
* **User Stories Implemented:** Vendor - View sales records & best-selling items
* **Permitted Personas:** Food Vendor.
* **Expected Behaviour:** The system aggregates completed orders to display daily total revenue and item-level sales volumes.
* **Key Data:** Order (OrderID, DinerID, VendorID, TotalAmount, OrderStatus); OrderItem (OrderItemID, OrderID, MenuItemID, Quantity, UnitPrice); Payment (PaymentID, OrderID, PaymentStatus).
* **Data Relationships:** Each Order contains one or more OrderItem records and has an associated Payment record. OrderItem records identify the items sold, while PaymentStatus determines whether the transactions is included in sales calculations. 
* **Rules & Validation:** Only orders with a PaymentStatus of 'Success' are included in the revenue calculations.
* **Workflow:** Vendor navigates to Dashboard --> System aggregates completed orders (PaymentStatus = 'Success') --> Renders key metric cards (Total Revenue, Total Orders), a peak-hour sales trend chart, and a ranked list of top-selling menu items.
  
### Q4(b)

* **Usability & Accessibility:** The diner ordering interface must achieve a 90% order-completion rate for users aged 55+ on their first attempt without external assistance. This requirement can be tested by conducting usability testing with users aged 55+ and recording whether they can successfully browse, select items, review their order and proceed to checkout without assistance. Based on the survey, it was revealed that diners aged 55+ have the lowest willingness to adopt the app (mean: 3.0). This shows that complex UI will result in lesser adoption by users from this demographic.
* **Performance:** The system must process payment gateway responses and generate the finalised OrderID in under 3 seconds. This can be verified by measuring response time from receipt of successful payment gateway until the OrderID is generated and displayed to the diner. Since the survey highlights extreme diner frustration with waiting (queue pain mean: 4.2), this means that slow digital checkout rendering would replicate the bottleneck of a physical queue, defeating the app's core purpose.
* **Reliability:** The vendor dashboard must poll and reflect new paid orders in under 5 seconds of successful payment completion. The system can be tested by recording the timestamp when the corresponding order becomes visible in the vendor dashboard. Since vendors currently face severe peak-hour stress leading to a 100% order error rate. Delayed order syncing will cause missed preparations and exacerbate counter arguments.
* **Security & Privacy:** The system must encrypt all payment token transmissions using TLS 1.3 and comply with PCI-DSS standards, ensuring zero local storage of raw credit card details or PayNow secret keys. This can be verified by inspecting the system's security configuration and database to confirm that payment transmissions use TLS 1.3 or stronger and that raw payment credentials are not stored in the database. The selected payment gateway's PCI-DSS compliance can be verified through its provider documentation. Since diners aged 55+ exhibited the lowest willingness to adopt the app (mean: 3.0). Enforcing strict industry-standard payment security mitigates potential security risks and ensures platform trust across all user demographics.

### Q4(c)

* **Split-Bill Payments:** Building a split-bill feature is too technically complex for a Version 1 launch. Diners can just transfer funds privately via PayNow.
* **Diner Order History:** Saving past orders for quick reordering is a retention feature that does not contribute to the essential baseline of processing a live food court transaction.
* **Food Delivery** SkipQ strictly focuses on resolving physical queue congestion for on-site diners. Delivery logistics add unnecessary operational complexity, and our survey data provided no evidence of delivery demand or rider feasibility.
* **Hawker Seat Reservation** SkipQ's primary scope is streamlining food transactions. While a minor subset of survey respondents mentioned seating issues, seat allocation is out of scope for a V1 transaction-focused system.
