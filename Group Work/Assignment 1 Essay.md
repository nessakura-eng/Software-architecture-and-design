## 1. Overall System Design

The proposed Bookstore System is designed as an object-oriented software architecture that models the major business capabilities required by a modern online bookstore. The architecture separates the system into functional areas for user/account management, catalog management, shopping carts, orders, returns, inventory, payments, and database backup.

The structure was derived from both academic research on online-bookstore software systems and an examination of major real-world online bookstores, specifically Barnes & Noble, Books-A-Million, and Amazon. Perera's online-bookstore architecture identifies core activities including searching for books, ordering books, making payments, and tracking orders through delivery. Pleskach et al. specifically investigate architectural solutions for software systems used for book sales and design an electronic bookstore. Vaidya et al. further demonstrate the use of object-oriented concepts such as encapsulation, inheritance, polymorphism, and design patterns in an online bookstore with a real-time database. These sources establish the major functional areas of the system, while the specific classes and design patterns in this diagram represent the chosen object-oriented implementation of those capabilities. [1][2][3]

The industry analysis supports these architectural decisions. Barnes & Noble provides book searching, multiple book formats, shopping carts, customer accounts, checkout, and orders. Books-A-Million similarly provides a shopping-cart workflow followed by account authentication, payment, shipping information, order review, and order submission. Amazon provides customer order management and return processing, including eligibility rules that vary by product type. [4][5][6]

The architecture therefore separates the bookstore into cohesive packages so that each major responsibility can be maintained independently while the classes communicate through well-defined relationships and interfaces.

---

# 2. User Package

The User package contains the classes responsible for identifying customers, managing accounts, controlling access, and supporting administrative responsibilities.

### `User`

`User` represents the general identity of a person interacting with the bookstore system. It provides the common information and behavior that can be shared by different types of users.

This class is justified because online bookstores require customers to maintain accounts and authenticate before performing certain activities. Barnes & Noble requires customers to use a bn.com account for purchasing eBooks and associates purchases with the customer's account. Books-A-Million also requires users to either create an account or sign into an existing account during checkout. [4][5]

### `Customer`

`Customer` specializes `User` for the individual purchasing books from the bookstore. The customer is responsible for activities such as browsing books, maintaining a cart, placing orders, making payments, and managing returns.

This specialization reflects the primary customer role in an online bookstore and allows customer-specific behavior to remain separate from administrative behavior.

### `BookstoreAdministrator`

`BookstoreAdministrator` represents a user responsible for managing bookstore-specific information such as books, catalog information, inventory, and bookstore operations.

A separate administrator role is appropriate because the bookstore must maintain its catalog and inventory independently of ordinary customer activities. Vaidya et al. identify the need to coordinate book catalogs, user databases, orders, and inventory within an online bookstore system. [3]

### `SystemAdministrator`

`SystemAdministrator` represents a higher-level administrative role responsible for system-level management rather than ordinary bookstore purchasing activities.

This class is an architectural design decision rather than a claim that every commercial bookstore exposes a class with this exact name. Separating system administration from bookstore administration follows the principle of separating responsibilities: bookstore administrators manage bookstore content and operations, while system administrators can manage broader system-level functions.

### `AccountManager`

`AccountManager` centralizes account-related operations such as creating, updating, authenticating, and recovering user accounts.

This separation prevents account-management logic from being duplicated across `Customer`, administrators, and other system components. It also reflects the authentication and account-management capabilities present in actual online bookstores. Barnes & Noble requires account authentication for purchases, while Books-A-Million supports account creation and sign-in as part of checkout. [4][5]

### `AccountAccess`

`AccountAccess` is an interface defining the operations that clients use to access account functionality.

The interface separates the definition of account operations from their implementation. This supports abstraction and makes it possible to change the underlying access-control implementation without changing every class that depends on account access.

### `SecureAccountProxy`

`SecureAccountProxy` implements the Proxy design pattern. It controls access to account functionality and provides a location for security checks before allowing operations to proceed.

The Proxy is useful because account information and account operations are security-sensitive. Rather than allowing every client to directly access the account implementation, the proxy can verify authentication and authorization first.

The commercial bookstore sources establish the need for authenticated customer accounts, but they do not establish that Barnes & Noble, Books-A-Million, or Amazon internally use the Proxy pattern. Therefore, the Proxy is a software-design decision made to satisfy the security requirement rather than a claim about the internal architecture of those companies.

### `PasswordResetToken`

`PasswordResetToken` represents the temporary credential used when a customer needs to recover account access.

Password recovery is a real-world account requirement, while the token class is the proposed implementation mechanism. The class isolates password-reset information from the permanent user account and supports safer temporary authorization.

---

# 3. Backup Package

The Backup package addresses persistence and recovery of bookstore data.

### `Database`

`Database` represents persistent storage for the bookstore's users, books, inventory, carts, orders, payments, and returns.

A database is essential because the bookstore must retain information beyond an individual session. Vaidya et al. specifically develop an online bookstore using a real-time database and identify database management, inventory synchronization, users, books, and orders as important system concerns. [3]

### `DatabaseSnapshot`

`DatabaseSnapshot` represents a saved state of the database that can be used for recovery.

The need for persistent bookstore data is supported by the online-bookstore literature, but the specific `DatabaseSnapshot` class is a reliability design decision. It was included to provide a mechanism for restoring the bookstore's persistent data if the active database becomes corrupted or unavailable.

This class therefore represents an infrastructure/reliability concern rather than a bookstore business-domain object.

---

# 4. Catalog Package

The Catalog package represents the products that the bookstore sells and provides mechanisms for customers to locate them.

### `Book`

`Book` is the central product class in the system. It contains the common information and behavior shared by the different forms of books sold by the bookstore.

This is directly supported by online-bookstore research. Vaidya et al. identify `Book` as one of the core domain classes and explain that an online bookstore must accommodate different book types. Pleskach et al. likewise focus specifically on software systems for book sales. [2][3]

### `PhysicalBook`

`PhysicalBook` represents a physical copy of a book, such as a hardcover or paperback.

It is separated from the general `Book` class because physical books have properties and business processes that digital books do not, including physical inventory, shipping, and physical returns.

This distinction is observable in real online bookstores because retailers sell books in multiple physical formats as well as digital formats. Barnes & Noble also explicitly distinguishes book formats and provides different purchasing workflows for digital content. [4]

### `EBook`

`EBook` represents a digitally delivered book.

The subclass is necessary because an eBook has different fulfillment and ownership behavior from a physical book. Barnes & Noble's eBook workflow requires selecting the eBook format, purchasing it, associating it with the customer's account, and making it available through the customer's NOOK Library. [4]

Vaidya et al. also specifically identify eBooks as one of the product types that an online-bookstore architecture must accommodate. [3]

Therefore:

`Book` → `PhysicalBook` / `EBook`

uses inheritance because the two product types share common book attributes while requiring different specialized behavior.

### `Search`

`Search` represents the mechanism through which customers locate books in the catalog.

Search is a fundamental online-bookstore capability. Perera's online-bookstore example explicitly begins with users searching for books, and Barnes & Noble allows customers to locate books by searching by title, author, or subject. [1][4]

Separating `Search` from `Book` follows separation of responsibilities: a book represents the product, while search represents the operation used to find products.

---

# 5. Cart Package

The Cart package manages books selected by a customer before an order is submitted.

### `Cart`

`Cart` represents the customer's current collection of products selected for purchase.

A shopping cart is a fundamental component of the online bookstore workflow. Perera explicitly includes a shopping cart in the online-bookstore architecture. Barnes & Noble allows users to add books to a Shopping Cart before checkout, and Books-A-Million explicitly describes its website as using a shopping-cart system in which users can add, remove, and modify quantities of items. [1][4][5]

### `CartItem`

`CartItem` represents one particular book and its associated quantity within a cart.

Separating `CartItem` from `Cart` allows one cart to contain multiple different products while allowing each product to have its own quantity and associated information.

This corresponds directly to the real-world behavior documented by Books-A-Million, where customers can place items into the cart and change the quantity of an individual item before checkout. [5]

---

# 6. Order Package

The Order package converts a customer's intended purchase into a formal bookstore transaction and tracks its progression.

### `Order`

`Order` represents a completed purchase transaction.

Online-bookstore research consistently identifies ordering as a core capability. Perera specifically describes users ordering books, paying for them, and tracking them through delivery. Barnes & Noble generates an order number after an eBook purchase, while Books-A-Million generates an Order ID after the order is submitted. [1][4][5]

### `OrderItem`

`OrderItem` represents an individual book and quantity included in an order.

This class separates the overall transaction from the individual products being purchased. One order can therefore contain multiple order items.

### `OrderService`

`OrderService` coordinates operations involving the creation and processing of orders.

The service separates order-processing logic from the `Order` data object. This keeps the `Order` class focused on representing the transaction while the service handles operations performed on that transaction.

This decomposition is consistent with the service-oriented approach discussed in modern online-bookstore architecture research. [1][2]

---

## 6.1 Order State Pattern

The order lifecycle is represented through the `OrderState` interface and its concrete states:

- `PendingState`
- `PaidState`
- `ShippedState`
- `DeliveredState`

### `OrderState`

`OrderState` defines the common behavior associated with the current state of an order.

The State pattern is appropriate because the behavior of an order depends on where it is in its lifecycle. An order that has not been paid should not behave identically to an order that has already shipped.

### `PendingState`

Represents an order that has been created but has not completed the required payment/processing steps.

### `PaidState`

Represents an order for which payment has been successfully completed.

### `ShippedState`

Represents an order that has entered the shipping/fulfillment stage.

### `DeliveredState`

Represents an order that has reached the customer.

Perera's online-bookstore example explicitly describes the sequence of ordering, payment, and tracking the order until delivery. Barnes & Noble and Books-A-Million similarly provide order confirmation and order-management functionality. [1][4][5]

Therefore, the State pattern was selected to represent the **business lifecycle** of an order rather than placing numerous conditional statements inside `Order`.

The sources establish the need for ordering, payment, and delivery tracking; the State classes are the chosen software implementation of those business states.

---

# 7. Return Package

The Return package manages the process of returning a purchased book and determining whether the return is permitted.

### `Return`

`Return` represents a customer's request to return a purchased item.

Returns are a recognized component of online-bookstore systems. Perera includes returns among the services associated with the online bookstore architecture. Amazon's actual customer workflow allows users to select an order, select an item to return, provide a reason, and choose how the return should be processed. [1][6]

### `ReturnService`

`ReturnService` coordinates return-related operations.

It separates the return-processing workflow from the `Return` data object.

### `ReturnEligibility`

`ReturnEligibility` defines the rules used to determine whether an item can be returned.

This interface is important because return eligibility can depend on the type of product and the circumstances of the purchase.

Amazon's current return policy demonstrates that return eligibility is not universally identical: different categories can have different return windows, and digital books have specific return rules. [6]

### `PhysicalBookReturnPolicy`

This policy contains return rules applicable to physical books.

Physical books can involve shipping, physical possession, condition requirements, and return shipment.

### `EBookReturnPolicy`

This policy contains return rules applicable to digital books.

The distinction is justified by real-world bookstore behavior. Amazon specifically identifies a separate return window for accidentally purchased digital books that have not been read, while Barnes & Noble handles eBooks through its digital-content/account system. [4][6]

Therefore, the Strategy/Policy-style abstraction represented by `ReturnEligibility` allows the bookstore to apply different rules without placing every return rule inside the `Return` class.

---

## 7.1 Return State Pattern

The return process is represented by:

- `ReturnState`
- `PendingReturnState`
- `ApprovedState`
- `DeniedState`

### `ReturnState`

Defines the common behavior of a return at different stages of processing.

### `PendingReturnState`

Represents a submitted return that has not yet been evaluated.

### `ApprovedState`

Represents a return that has been accepted.

### `DeniedState`

Represents a return that has been rejected because it does not meet the applicable eligibility requirements.

Amazon's return workflow demonstrates that a return request can be initiated, evaluated, and either processed for refund/replacement or handled differently depending on eligibility. [6]

The State pattern therefore provides a clean representation of the return workflow without requiring one large `Return` class containing all possible status conditions.

---

# 8. Inventory Package

The Inventory package manages the availability of books.

### `Inventory`

`Inventory` represents the quantities and availability of books maintained by the bookstore.

Inventory is a critical bookstore capability because the system must know whether a physical book can be purchased, shipped, or otherwise fulfilled. Vaidya et al. specifically identify poor inventory management and lack of continuous inventory updates as problems in online bookstore systems. Their system uses real-time inventory management to address these problems. [3]

The relationship between `Inventory` and `Book` therefore allows the system to associate a book with its current availability information.

### `Observer`

`Observer` is the interface used for components that need to be notified when inventory information changes.

Vaidya et al. explicitly discuss real-time updates and identify the Observer pattern as the mechanism used for real-time updating in their online-bookstore system. [3]

### `LowStockNotifier`

`LowStockNotifier` is a concrete observer that reacts when inventory falls below a defined threshold.

The purpose is to separate the inventory-management responsibility from the notification responsibility. `Inventory` changes the stock information, while `LowStockNotifier` responds to that change.

The Observer pattern prevents the inventory class from becoming tightly coupled to every possible notification mechanism.

---

# 9. Payment Package

The Payment package separates the bookstore's payment process from the individual payment methods and external payment provider.

### `PaymentMethod`

`PaymentMethod` represents the abstraction for a method through which a customer can pay.

This abstraction is appropriate because online bookstores must support payment processing while allowing the specific payment mechanism to vary.

Vaidya et al. specifically identify extensibility to new payment methods as a benefit of inheritance and polymorphism in an online bookstore system. [3]

### `Cash`

`Cash` represents cash as a possible payment implementation.

This is a design choice allowing the system to represent different payment methods through the common `PaymentMethod` abstraction. Whether cash is actually offered in a particular online-only deployment would be a business constraint.

### `CreditCard`

`CreditCard` represents payment using a credit card.

Credit-card payment is directly observable in real online bookstore systems. Barnes & Noble documents the use of a stored credit card for its Instant Purchase functionality, and Books-A-Million requires payment information during checkout. [4][5]

### `DebitCard`

`DebitCard` represents debit-card payment as another implementation of the common payment abstraction.

### `GiftCard`

`GiftCard` represents payment using a bookstore gift card or electronic gift certificate.

Books-A-Million explicitly supports applying a gift card or eGift certificate during checkout. [5]

The three card/payment classes therefore demonstrate polymorphism: each implements the common payment concept while allowing payment-specific behavior.

---

## 9.1 Payment Factory

### `PaymentFactory`

`PaymentFactory` is used to create the appropriate `PaymentMethod` implementation without requiring the rest of the system to directly instantiate concrete payment classes.

For example, the client can request a credit-card payment without needing to know how the `CreditCard` object is constructed.

Vaidya et al. discuss Factory as an OOP design-pattern approach and emphasize extensibility to additional payment methods. [3]

The Factory therefore supports:

- extensibility,
- reduced coupling,
- centralized object creation,
- easier addition of future payment methods.

---

## 9.2 Payment Gateway

### `PaymentGateway`

`PaymentGateway` provides an abstraction between the bookstore and the external service responsible for processing payments.

This prevents the rest of the bookstore from becoming dependent on a specific payment provider.

### `StripeGatewayAdapter`

`StripeGatewayAdapter` implements the Adapter pattern.

The adapter converts the bookstore's internal `PaymentGateway` interface into the interface expected by Stripe.

This is important because the bookstore should depend on its own abstraction rather than having `Order` or other domain classes directly depend on Stripe-specific API calls.

### `StripeAPI`

`StripeAPI` represents the external payment service used by the bookstore.

Stripe is therefore an implementation choice rather than a requirement established by the bookstore sources. The academic and industry sources establish that online bookstores require payment processing; the choice of Stripe is the system's selected external payment provider.

The resulting structure is:

`PaymentFactory → PaymentMethod`

and

`PaymentGateway → StripeGatewayAdapter → StripeAPI`

This separates the internal bookstore payment model from the external payment provider.

---

# 10. Relationships Between the Major Packages

The packages work together to represent the complete bookstore purchasing lifecycle.

### User → Catalog

A `Customer` uses `Search` to locate `Book` objects in the catalog.

### Customer → Cart

A `Customer` maintains a `Cart`, and the cart contains `CartItem` objects representing the books selected for purchase.

### Cart → Order

When the customer checks out, the selected cart information is used to create an `Order` containing one or more `OrderItem` objects.

### Order → Payment

The `OrderService` coordinates payment processing. The selected `PaymentMethod` is created through `PaymentFactory`, while `PaymentGateway` abstracts communication with the external payment provider.

### Order → Inventory

The order references books whose availability is maintained by `Inventory`. This relationship prevents the system from treating a book as available without corresponding inventory information.

### Inventory → Observer

When inventory changes, the `Observer` mechanism allows `LowStockNotifier` to respond without tightly coupling notification behavior to inventory-management logic.

### Order → OrderState

The order's current lifecycle is represented through `OrderState`, allowing the order to transition from pending to paid, shipped, and delivered.

### Order → Return

After an order has been placed, the customer can initiate a `Return`. `ReturnService` processes the return, while `ReturnEligibility` determines whether the particular book is eligible.

### Return → ReturnState

The return progresses through pending, approved, or denied states.

### Book → PhysicalBook/EBook

`PhysicalBook` and `EBook` inherit from `Book` because they share common book characteristics but require different fulfillment and business behavior.

### User → AccountAccess

Account operations are accessed through the `AccountAccess` abstraction, while `SecureAccountProxy` controls access to those operations.

### Database → System

The database provides persistent storage for the system's users, books, inventory, carts, orders, payment information, and return information. `DatabaseSnapshot` provides a recovery mechanism for persistent data.

---

# 11. Design Patterns Used

The diagram uses several design patterns because different parts of the bookstore have different architectural problems.

### Inheritance

Used for:

`User → Customer / BookstoreAdministrator / SystemAdministrator`

and

`Book → PhysicalBook / EBook`

Inheritance is appropriate where subclasses share common attributes and behavior but have specialized responsibilities. Vaidya et al. specifically identify inheritance and polymorphism as useful for accommodating new book types and user roles in online-bookstore software. [3]

### State Pattern

Used for:

`OrderState → PendingState / PaidState / ShippedState / DeliveredState`

and

`ReturnState → PendingReturnState / ApprovedState / DeniedState`

The State pattern is used because orders and returns have distinct business states whose behavior can change depending on their current state.

### Observer Pattern

Used for:

`Inventory → Observer → LowStockNotifier`

The Observer pattern allows the system to respond to inventory changes without tightly coupling inventory management to notification behavior. Vaidya et al. specifically discuss Observer for real-time updating in an online bookstore system. [3]

### Factory Pattern

Used for:

`PaymentFactory → PaymentMethod`

The Factory pattern centralizes creation of payment-method objects and allows additional payment methods to be added without modifying every class that creates payments.

### Adapter Pattern

Used for:

`PaymentGateway → StripeGatewayAdapter → StripeAPI`

The Adapter pattern isolates the bookstore's internal payment abstraction from the external Stripe API.

### Proxy Pattern

Used for:

`AccountAccess → SecureAccountProxy`

The Proxy provides an intermediary for account access so that authentication and authorization checks can be performed before sensitive account operations are executed.

### Policy/Strategy-style abstraction

Used for:

`ReturnEligibility → PhysicalBookReturnPolicy / EBookReturnPolicy`

The abstraction allows return rules to vary according to the type of product without placing every possible rule inside the `Return` class.

---

# 12. Why the Diagram Is Structured This Way

The overall design follows the principle of assigning each class a focused responsibility.

The major domain objects represent the bookstore itself:

- `User`
- `Book`
- `Cart`
- `Order`
- `Inventory`
- `Return`
- `Payment`

The service classes coordinate operations:

- `AccountManager`
- `OrderService`
- `ReturnService`

The interfaces provide abstraction:

- `AccountAccess`
- `OrderState`
- `ReturnEligibility`
- `ReturnState`
- `Observer`
- `PaymentMethod`
- `PaymentGateway`

The concrete classes implement specialized behavior:

- `Customer`
- `PhysicalBook`
- `EBook`
- `PendingState`
- `PaidState`
- `ShippedState`
- `DeliveredState`
- `PhysicalBookReturnPolicy`
- `EBookReturnPolicy`
- `PendingReturnState`
- `ApprovedState`
- `DeniedState`
- `LowStockNotifier`
- `CreditCard`
- `DebitCard`
- `GiftCard`
- `Cash`
- `StripeGatewayAdapter`

This organization improves maintainability because a change to one area of the bookstore does not require unrelated classes to contain that area's logic.

Vaidya et al. specifically identify modularity, extensibility, inheritance, polymorphism, and maintainability as advantages of the object-oriented approach for an online bookstore. [3]

---

# 13. Why These Classes Are Necessary for the Bookstore

The diagram can be summarized as seven major capabilities:

| CapabilityClasses               |                                                                                                                                                    |
| ------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Customer/account management** | `User`, `Customer`, `BookstoreAdministrator`, `SystemAdministrator`, `AccountManager`, `AccountAccess`, `SecureAccountProxy`, `PasswordResetToken` |
| **Book catalog/discovery**      | `Book`, `PhysicalBook`, `EBook`, `Search`                                                                                                          |
| **Shopping**                    | `Cart`, `CartItem`                                                                                                                                 |
| **Ordering**                    | `OrderService`, `Order`, `OrderItem`, `OrderState` and states                                                                                      |
| **Returns**                     | `ReturnService`, `Return`, `ReturnEligibility`, return policies, return states                                                                     |
| **Inventory**                   | `Inventory`, `Observer`, `LowStockNotifier`                                                                                                        |
| **Payment**                     | `PaymentFactory`, `PaymentMethod`, payment implementations, `PaymentGateway`, `StripeGatewayAdapter`, `StripeAPI`                                  |
| **Persistence/recovery**        | `Database`, `DatabaseSnapshot`                                                                                                                     |

These capabilities correspond to the actual online-bookstore workflow observed in the academic and industry sources: a customer accesses an account, searches for books, selects a physical or digital book, adds it to a cart, checks out, provides payment and shipping information when applicable, receives an order, tracks the order, and may subsequently initiate a return. Barnes & Noble demonstrates the search → format selection → cart → checkout → account → order workflow for eBooks; Books-A-Million documents the cart → checkout → payment/shipping → order-submission workflow; and Amazon demonstrates order-based return processing and product-dependent return eligibility. [4][5][6]

---

# 14. Important Scope of the Evidence

The academic and industry sources support the **bookstore capabilities represented by the diagram**, but they should not be interpreted as saying that Barnes & Noble, Books-A-Million, Amazon, or the academic systems use these exact class names or exact design patterns internally.

For example, the sources directly support the existence of:

- users/accounts,
- books,
- physical/digital book types,
- search,
- carts,
- orders,
- inventory,
- payments,
- returns,
- databases,
- order tracking.

The more specific classes such as `PaymentFactory`, `StripeGatewayAdapter`, `SecureAccountProxy`, `DatabaseSnapshot`, and `LowStockNotifier` are **architectural design decisions made for this proposed system**. Their purpose is to provide a clean object-oriented implementation of the requirements established by the bookstore domain.

This distinction is important because a public bookstore website exposes its functionality, but it does not expose its complete internal software architecture.

Therefore, the sources establish **why the bookstore needs the capability**, while the UML design explains **how this particular system implements that capability**.

---

# 15. Conclusion

The proposed UML class diagram models the complete purchasing lifecycle of an online bookstore while separating the system into cohesive responsibilities. The Catalog package manages books and search, the User package manages customers and administrative access, the Cart package manages items selected for purchase, the Order package manages purchases and their lifecycle, the Inventory package manages book availability, the Payment package manages multiple payment mechanisms and external payment processing, the Return package manages return eligibility and processing, and the Backup package provides persistent-data recovery.

The architecture is supported by recent online-bookstore research and by examination of established real-world online bookstores. Perera provides a direct architectural model for an online bookstore involving search, ordering, payment, and order tracking. Pleskach et al. specifically examine architectural solutions for book-sale software systems. Vaidya et al. provide direct object-oriented support for representing books, users, orders, inventory, payment methods, inheritance, polymorphism, and design patterns in an online bookstore. [1][2][3]

The examination of Barnes & Noble, Books-A-Million, and Amazon further validates that the major capabilities represented in the architecture exist in real-world bookstore systems. [4][5][6]

Consequently, the diagram is not simply a collection of arbitrary classes. The domain classes are derived from the recurring requirements of online bookstores, while the interfaces, services, inheritance structures, and design patterns provide an object-oriented mechanism for implementing those requirements in a modular, maintainable, and extensible manner.

## References

[1] S. Perera, “Designing for an Online Bookstore,” in *Software Architecture and Decision-Making: Leveraging Leadership, Technology, and Product Management to Build Great Products*, 2024. [Online bookstore architecture source](https://www.informit.com/articles/article.aspx?p=3192420&seqNum=5&utm_source=chatgpt.com)

[2] V. Pleskach, Y. Kryvolapov, H. Kryvolapov, and O. Zelikovska, “Investigating E-Commerce Systems for Book Sales: From Theoretical Foundations to Software Development,” in *Information Technology and Implementation (IT&I 2024)*, pp. 264–274, 2024. [Bibliographic record](https://dblp.org/rec/conf/iti2/PleskachKKZ24?utm_source=chatgpt.com)

[3] H. Vaidya, A. R. Nayani, A. Gupta, P. Selvaraj, and R. K. Singh, “Using OOP Concepts for the Development of a Web-Based Online Bookstore System with a Real-Time Database,” *International Journal for Research Publication and Seminar*, vol. 14, no. 5, pp. 253–274, 2023, doi: 10.36676/jrps.v14.i5.1502. [Publisher/source page](https://jrps.shodhsagar.com/index.php/j/article/view/1502?utm_source=chatgpt.com)

[4] Barnes & Noble, “Buying eBooks and Other Digital Content,” Barnes & Noble Customer Help Desk, 2025. [Barnes & Noble source](https://help.barnesandnoble.com/hc/en-us/articles/5398798012571-Buying-eBooks-and-Other-Digital-Content?utm_source=chatgpt.com)

[5] Books-A-Million, “How Does the Books-A-Million Ordering Process Work?,” Books-A-Million Customer Help Desk, updated 2025. [Books-A-Million source](https://support.booksamillion.com/hc/en-us/articles/360055107494-How-Does-the-Books-a-Million-Ordering-Process-Work?utm_source=chatgpt.com)

[6] Amazon, “Return Items You Ordered” and “Amazon Return Policy,” Amazon Customer Service, accessed 2026. [Amazon return source](https://digprjsurvey.amazon.com/csad/help/node/G6E3B2E8QPHQ88KF?utm_source=chatgpt.com) [Amazon return-policy source](https://digprjsurvey.amazon.com/csad/help/node/GKM69DUUYKQWKWX7?utm_source=chatgpt.com)
