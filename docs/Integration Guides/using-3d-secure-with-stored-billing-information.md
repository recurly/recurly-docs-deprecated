---
title: Using 3D secure with stored Billing Information
deprecated: false
hidden: true
metadata:
  robots: index
---
# Overview

This guide shows you how to use the [Purchase endpoint](https://developers.recurly.com/api/latest/#tag/purchase) to create new accounts, add subscriptions or one-time charges, and securely capture payment details. We’ll also illustrate how to integrate [Recurly.js](https://developers.recurly.com/reference/recurly-js) for secure tokenization.

### Prerequisites & limitations

* Familiarity with Recurly’s API and basic REST concepts
* [Completed the Quickstart Guide](https://docs.recurly.com/v1.1/docs/quick-start-guide#/) and [3DS integration guide](https://docs.recurly.com/v1.1/docs/3d-secure-20-integration-guide#/versions)
* Conditional usage of [Recurly.js](https://developers.recurly.com/reference/recurly-js) depending on your supported gateway:
  * Cybersource and WorldPay gateways require use of Recurly.js to complete this integration guide

***

# Definition

**Stored Billing Information** refers to card data stored in Recurly's systems and referenced via API by sending in an account code, or specify a billing ID when using Recurly Wallet.

**PSD2** refers to the EU mandate for SCA, or Strong Customer Authentication, and is applicable to EU merchants mostly. Read more about [PSD2 in our compliance documentation](https://docs.recurly.com/docs/revised-payment-services-directive-psd2#/) for 3DS, SCA, and PSD2.

**Reactivating** a cancelled Subscription is referring to the API path in Recurly APIs where a customer's cancelled subscription is reactivated and resumes renewal billing again. You can view documentation on [reactivating a cancelled subscription via API](https://recurly.com/developers/api/v2021-02-25/index.html#operation/reactivate_subscription) in our developer hub.

**Resuming** a paused Subscription is referring to the API path in Recurly APIs where a customer's paused subscription is resumed and starts billing again. You can view documentation on [resuming a paused subscription via API](https://recurly.com/developers/api/v2021-02-25/index.html#operation/resume_subscription) in our developer hub.

***

# 3DS prior to Resuming a Paused Subscription

## Step 1: Submit a Verification Request via API

When a customer who has a paused subscription requests that subscription is resumed, prior to resuming that subscription, use the API to request billing info verification using an account code, or a billing info ID if using Recurly Wallet, that is attached to that subscription.

You can do this in one of two ways depending on your preference:

* With CVV: [https://recurly.com/developers/api/v2021-02-25/index.html#operation/verify\_billing\_info\_cvv](https://recurly.com/developers/api/v2021-02-25/index.html#operation/verify_billing_info_cvv)
* Without CVV: [https://recurly.com/developers/api/v2021-02-25/index.html#operation/verify\_billing\_info](https://recurly.com/developers/api/v2021-02-25/index.html#operation/verify_billing_info)

Use [Recurly.js](https://developers.recurly.com/reference/recurly-js/#getting-started) to embed secure payment fields in your front-end code. This library collects customer card data and returns a **token** (`rjs_token_id`) representing the payment details. You’ll use this token when making your purchase request to Recurly.

***

## Step 2: Create a purchase request

Send a request to the `create_purchase` method on Recurly’s API, including:

* **Customer account data** (e.g., code, name, billing info)
* **Subscriptions** (with plan codes)
* **Optional** one-time line items or charges

Below are example calls in different languages:

```ruby
purchase = {
  currency: "USD",
  account: {
    code: "bdumonde",
    first_name: "Benjamin",
    last_name: "Du Monde",
    billing_info: {
      token_id: rjs_token_id
    },
  },
  subscriptions: [
    { plan_code: "coffee-monthly" }
  ]
}

invoice_collection = @client.create_purchase(body: purchase)
```
```javascript
let purchaseReq = {
  currency: 'USD',
  account: {
    code: 'bdumonde',
    firstName: 'Benjamin',
    lastName: 'Du Monde',
    billingInfo: {
      tokenId: rjsTokenId
    }
  },
  subscriptions: [
    { planCode: 'coffee-monthly' }
  ]
}
let invoiceCollection = await client.createPurchase(purchaseReq)
```
```python
purchase = {
    "currency": "USD",
    "account": {
        "code": "bdumonde",
        "first_name": "Benjamin",
        "last_name": "Du Monde",
        "billing_info": {"token_id": rjs_token_id},
    },
    "subscriptions": [{"plan_code": "coffee-monthly"}],
}
invoice_collection = client.create_purchase(purchase)
```
```java
PurchaseCreate purchase = new PurchaseCreate();
purchase.setCurrency("USD");

AccountPurchase account = new AccountPurchase();
account.setCode("bdumonde");
account.setFirstName("Benjamin");
account.setLastName("Du Monde");
purchase.setAccount(account);

BillingInfoCreate billing = new BillingInfoCreate();
billing.setTokenId(rjsTokenId);
account.setBillingInfo(billing);

List<SubscriptionPurchase> subs = new ArrayList<>();
SubscriptionPurchase sub = new SubscriptionPurchase();
sub.setPlanCode("coffee-monthly");
subs.add(sub);
purchase.setSubscriptions(subs);

InvoiceCollection collection = client.createPurchase(purchase);
```
```csharp
var purchaseReq = new PurchaseCreate()
{
    Currency = "USD",
    Account = new AccountPurchase()
    {
        Code = "bdumonde",
        FirstName = "Benjamin",
        LastName = "Du Monde",
        BillingInfo = new BillingInfoCreate()
        {
            TokenId = rjsTokenId
        }
    },
    Subscriptions = new List<SubscriptionPurchase>()
    {
        new SubscriptionPurchase() { PlanCode = "coffee-monthly" }
    }
};

InvoiceCollection collection = client.CreatePurchase(purchaseReq);
```

> **Tip:** Many more parameters are available. See the [Create Purchase](https://developers.recurly.com/api/latest/#operation/create_purchase) reference to learn more.

***

## Step 3: Process the purchase response

A successful purchase returns an **InvoiceCollection**, which contains any charge or credit invoices generated by the request. If the purchase fails, you’ll receive an error response indicating what went wrong.

```ruby
invoice = invoice_collection.charge_invoice
puts "Created Invoice #{invoice}"
```
```js
let invoice = invoiceCollection.chargeInvoice
console.log('Created Invoice:', invoice)
```
```python
invoice = invoice_collection.charge_invoice
print("Created Invoice %s" % invoice)
```
```java
Invoice invoice = collection.getChargeInvoice();
System.out.println("Created Charge Invoice with Id: " + invoice.getId());
```
```csharp
Invoice invoice = collection.ChargeInvoice;
Console.WriteLine($"Created Invoice with Number: {invoice.Number}");
```

***

## Step 4: Verify and finish

After a successful purchase, you can confirm the details via the Recurly Admin UI or by calling Recurly’s API to list your new account, subscription, or invoice.

***

## Next steps

Now that you can create new [accounts](https://app.recurly.com/go/accounts), [subscriptions](https://app.recurly.com/go/subscriptions), and one-time payments, explore the [Subscription Management](https://docs.recurly.com/v1.1/docs/managing-subscription-methods-guides#/) guide to learn how to modify subscriptions after the initial purchase.