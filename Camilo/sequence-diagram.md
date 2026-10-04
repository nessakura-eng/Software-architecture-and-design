```mermaid 
sequenceDiagram
    autonumber
    actor Cust as Customer
    participant UI as Store UI
    participant Cart as ShoppingCart
    participant Prod as Product
    participant OrdCtrl as Order
    participant OrdItem as OrderItem
    participant Pay as Payment
    participant CCPay as CreditCardPayment
    participant DB as StoreDB

    Cust->>UI: browseProducts()
    activate UI
    UI->>Prod: getProductList()
    activate Prod
    Prod-->>UI: returnProductList()
    deactivate Prod
    UI-->>Cust: displayProducts()
    deactivate UI

    Cust->>UI: addToCart(productId)
    activate UI
    UI->>Cart: addItem(productId)
    activate Cart
    Cart->>Prod: getProduct(productId)
    activate Prod
    Prod-->>Cart: returnProduct()
    deactivate Prod
    Cart-->>UI: confirmItemAdded()
    deactivate Cart
    UI-->>Cust: updateCartDisplay()
    deactivate UI

    Cust->>UI: proceedToCheckout()
    activate UI
    UI->>OrdCtrl: createOrderFromCart()
    activate OrdCtrl
    OrdCtrl->>Cart: getCartItems()
    activate Cart
    Cart-->>OrdCtrl: returnItems()
    deactivate Cart
    OrdCtrl->>OrdItem: createOrderItems(items)
    activate OrdItem
    OrdItem-->>OrdCtrl: itemsCreated()
    deactivate OrdItem
    OrdCtrl-->>UI: orderCreated(total)
    deactivate OrdCtrl
    UI-->>Cust: displayCheckoutForm()
    deactivate UI

    Cust->>UI: enterCreditCardInfo()
    activate UI
    UI->>Pay: processPayment(orderId, cardInfo)
    activate Pay

    alt payment approved
        Pay->>Pay: authorizePayment(cardInfo, amount)
        Pay->>CCPay: processCreditCard(cardInfo, amount)
        activate CCPay
        CCPay->>DB: saveTransaction()
        activate DB
        DB-->>CCPay: transactionSaved()
        deactivate DB
        CCPay-->>Pay: returnAuthorizationResult(Approved)
        deactivate CCPay
        
        Pay->>OrdCtrl: paymentSuccessful()
        activate OrdCtrl
        OrdCtrl->>DB: updateOrderStatus("Confirmed")
        activate DB
        DB-->>OrdCtrl: statusUpdated()
        deactivate DB
        OrdCtrl-->>Pay: orderConfirmed()
        deactivate OrdCtrl
        
        Pay-->>UI: returnSuccess()
        UI-->>Cust: displayConfirmation()

    else payment declined
        Pay->>CCPay: processCreditCard(cardInfo, amount)
        activate CCPay
        CCPay-->>Pay: returnAuthorizationResult(Declined)
        deactivate CCPay
        Pay-->>UI: paymentFailed()
        UI-->>Cust: displayError("Payment declined. Please try another card.")
    end

    deactivate Pay
    deactivate UI
