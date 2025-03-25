---
title: Coupons and discounts guide
deprecated: false
hidden: false
metadata:
  robots: index
---
## Overview

Everyone loves a good coupon! This guide will walk you through the programmatic process of creating and processing coupons using the Recurly API. You can generate single coupon codes for mass distribution or create many unique coupons for individual delivery and tracking.

Before continuing, it's recommended to review the [Quickstart Guide](/developers/guides/quickstart.html) and the [Subscription Management Guide](/developers/guides/manage-subscription.html).

## Step 1: Coupon creation

### Create a single coupon for mass distribution

Use the [Create Coupon](/developers/api/latest/index.html#operation/create_coupon) endpoint to create a single coupon code for mass distribution to many customers. This endpoint allows you to specify various properties, such as:

* **Name:** For internal tracking.
* **Maximum Redemptions:** Limits the number of redemptions overall or per customer account.
* **External Code:** Used by customers for redemption.
* **Discount Type:** Choose between a fixed amount, percentage, etc.

```ruby
coupon_create = {
  name: "Promotional Coupon",
  code: coupon_code,
  discount_type: 'fixed',
  currencies: [
    {
      currency: 'USD',
      discount: 10
    }
  ]
}
coupon = @client.create_coupon(
  body: coupon_create
)
puts "Created Coupon #{coupon}"
```
```js
let createCouponReq = {
  name: 'Promotional Coupon',
  code: coupon_code,
  discount_type: 'fixed',
  currencies: [
    {
      currency: 'USD',
      discount: 10
    }
  ]
}
let coupon = await client.createCoupon(createCouponReq)
```
```java
CouponCreate couponCreate = new CouponCreate();

couponCreate.setName("Promotional Coupon");
couponCreate.setCode(couponCode);

List<CouponPricing> currencies = new ArrayList<CouponPricing>();
CouponPricing couponPrice = new CouponPricing();
couponPrice.setCurrency("USD");
couponPrice.setDiscount(10.0f);
currencies.add(couponPrice);

couponCreate.setCurrencies(currencies);

Coupon coupon = client.createCoupon(couponCreate);
System.out.println("Created coupon " + coupon.getCode());
```
```python
coupon_create = {
    "name": "Promotional Coupon",
    "code": coupon_code,
    "discount_type": "fixed",
    "currencies": [{"currency": "USD", "discount": 10}],
}
coupon = client.create_coupon(coupon_create)
print("Created Coupon %s" % coupon)
```
```csharp
var createCouponReq = new CouponCreate()
{
    Name = "Promotional Coupon",
    Code = couponCode,
    DiscountType = "fixed",
    Currencies = new List<CouponPricing>()
    {
        new CouponPricing() { Currency = "USD", Discount = 10 }
    }
};
Coupon coupon = client.CreateCoupon(createCouponReq);
```

However, these are only a few of the coupon configurability options available. For more information, refer to the [reference documentation](/developers/api/latest/index.html#operation/create_coupon).

## Step 2: Coupon redemption

Customers can apply coupons to an initial purchase or to their account. When applied to an account, the coupon discount will be used in a future billing event.

### Redeem with purchase

To redeem one or more coupons as part of a new purchase, use the [Create Purchase](/developers/api/latest/index.html#operation/create_purchase) endpoint and pass in a list of coupon codes to be applied.

```ruby
purchase = {
  currency: "USD",
  account: {
    code: account_code,
    first_name: "Benjamin",
    last_name: "Du Monde",
    billing_info: {
      token_id: rjs_token_id
    },
  },
  subscriptions: [
    { plan_code: plan_code }
  ],
  coupon_codes: [
    "code_A",
    "code_B"
  ]
}
invoice_collection = @client.create_purchase(
  body: purchase
)
```
```js
let purchaseReq = {
  currency: 'USD',
  account: {
    code: accountCode,
    firstName: 'Benjamin',
    lastName: 'Du Monde',
    billingInfo: {
      tokenId: rjsTokenId
    }
  },
  subscriptions: [
    { planCode: planCode },
  ],
  coupon_codes: [
    "code_A",
    "code_B"
  ]  
}
let invoiceCollection = await client.createPurchase(purchaseReq)
```
```python
purchase = {
    "currency": "USD",
    "account": {
        "code": account_code,
        "first_name": "Benjamin",
        "last_name": "Du Monde",
        "billing_info": {"token_id": rjs_token_id},
    },
    "subscriptions": [{"plan_code": plan_code}],
    "coupon_codes": ["code_A", "code_B"]
}
invoice_collection = client.create_purchase(purchase)
```
```java
PurchaseCreate purchase = new PurchaseCreate();
purchase.setCurrency("USD");

AccountPurchase account = new AccountPurchase();
account.setCode(accountCode);
account.setFirstName("Benjamin");
account.setLastName("Eckel");
purchase.setAccount(account);

BillingInfoCreate billing = new BillingInfoCreate();
billing.setTokenId(rjsTokenId);
account.setBillingInfo(billing);

List<SubscriptionPurchase> subscriptions = new ArrayList<SubscriptionPurchase>();
SubscriptionPurchase sub = new SubscriptionPurchase();
sub.setPlanCode(planCode);
subscriptions.add(sub);
purchase.setSubscriptions(subscriptions);

List<String> couponCodes = Arrays.asList("code_A", "code_B");
purchase.setCouponCodes(couponCodes)

InvoiceCollection collection = client.createPurchase(purchase);
System.out.println("Created ChargeInvoice with Id: " + collection.getChargeInvoice().getId())
```
```csharp
var purchaseReq = new PurchaseCreate()
{
    Currency = "USD",
    Account = new AccountPurchase()
    {
        Code = accountCode,
        FirstName = "Benjamin",
        LastName = "Du Monde",
        BillingInfo = new BillingInfoCreate()
        {
            TokenId = rjsTokenId
        }
    },
    Subscriptions = new List<SubscriptionPurchase>()
    {
        new SubscriptionPurchase() { PlanCode = planCode }
    },
    CouponCodes = new List<string>{"code_A", "code_B"}
};
InvoiceCollection collection = client.CreatePurchase(purchaseReq);
```

### Apply a coupon to an account

To apply a coupon to a customer account, use the [Create Coupon Redemption](/developers/api/latest/index.html#operation/create_coupon_redemption) endpoint. Provide the ID of the coupon to be redeemed along with the associated currency.

> **Note:** that the `coupon_id` parameter can use either the primary key of the coupon or the `code-` prefix to identify the coupon. For example, the `coupon_id` for a coupon with the code `discount123` would be `code-discount123`.

```ruby
redemption = @client.create_coupon_redemption(
  account_id: account_id,
  body: {
    currency: 'USD',
    coupon_id: coupon_id
  }
)
puts "Created CouponRedemption #{redemption}"
```
```js
let couponRedemptionReq = {
    currency: 'USD',
    coupon_id: coupon_id
}
let couponRedemption = await client.createCouponRedemption(accountID, couponRedemptionReq)
```
```python
redemption_create = {"currency": "USD", "coupon_id": coupon_id}
redemption = client.create_coupon_redemption(account_id, redemption_create)
print("Created Redemption %s" % redemption)
```
```java
CouponRedemptionCreate coupRedCreate = new CouponRedemptionCreate();
coupRedCreate.setCouponId(couponId);
CouponRedemption redemption = client.createCouponRedemption(accountId, coupRedCreate);
System.out.println("Created coupon redemption " + redemption.getId());
```
```csharp
var couponRedemption = new CouponRedemptionCreate()
{
    Currency = "USD",
    CouponId = coupondId
};
CouponRedemption coupon = client.CreateCouponRedemption(accountId, couponRedemption);
```

## Step 3: Verify and Finish

You can use the API at any time to:

* Lookup a [specific coupon by ID](/developers/api/latest/index.html#operation/get_coupon).
* Obtain a [list of all coupons](/developers/api/latest/index.html#operation/list_coupons) created for your Recurly site/subdomain.
* Show [coupon redemptions](/developers/api/latest/index.html#operation/list_account_coupon_redemptions) for a specific account.
* Explore many [more options](/developers/api/latest/index.html#tag/coupon).