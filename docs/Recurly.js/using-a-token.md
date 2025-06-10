---
title: Using a token
excerpt: >-
  Use the Recurly token you generated with Recurly.js to create purchases,
  subscriptions, or update billing info via API v3—examples in every official
  client library plus key rules on token lifespan and security.
deprecated: false
hidden: false
metadata:
  robots: index
---
Once Recurly.js encrypts card data and returns a **token** (`token_id`), your server can pass that token to Recurly’s API in place of raw payment details. This page shows:

* Code samples (Ruby, Node, Python, Java, C#) for creating a purchase with a token
* Which API endpoints accept `token_id`
* Lifespan, reuse rules, and security best-practices

Tokens keep you out of PCI scope, but they **expire 20 minutes** after creation—so move them from browser → backend → Recurly quickly.

**Take into account**: 

- **Faster PCI compliance**: SAQ-A level—no sensitive data touches your servers.  
- **Consistent across endpoints**: The same `token_id` works for purchases, subscriptions, or standalone billing-info updates.  
- **Safe to retry**: Tokens can be reused within 20 minutes, simplifying idempotent flows and error recovery.

# Using a token to create a purchase

```ruby
purchase = {
  currency: "USD",
  account: {
    code: account_code,
    billing_info: { token_id: rjs_token_id }
  },
  subscriptions: [{ plan_code: plan_code }]
}
invoice_collection = @client.create_purchase(body: purchase)
```
```js node.js
const purchaseReq = {
  currency: 'USD',
  account: {
    code: accountCode,
    billingInfo: { tokenId: rjsTokenId }
  },
  subscriptions: [{ planCode }]
};
const invoiceCollection = await client.createPurchase(purchaseReq);
```
```python
purchase = {
    "currency": "USD",
    "account": {
        "code": account_code,
        "billing_info": {"token_id": rjs_token_id},
    },
    "subscriptions": [{"plan_code": plan_code}],
}
invoice_collection = client.create_purchase(purchase)
```
```java
PurchaseCreate purchase = new PurchaseCreate()
  .currency("USD")
  .account(new AccountPurchase()
      .code(accountCode)
      .billingInfo(new BillingInfoCreate().tokenId(rjsTokenId)))
  .subscriptions(List.of(new SubscriptionPurchase().planCode(planCode)));

InvoiceCollection collection = client.createPurchase(purchase);
```
```csharp
var purchaseReq = new PurchaseCreate {
  Currency = "USD",
  Account = new AccountPurchase {
    Code = accountCode,
    BillingInfo = new BillingInfoCreate { TokenId = rjsTokenId }
  },
  Subscriptions = new List<SubscriptionPurchase> {
    new SubscriptionPurchase { PlanCode = planCode }
  }
};
InvoiceCollection collection = client.CreatePurchase(purchaseReq);
```

***

## Token rules and security

| Rule          | Detail                                                                                          |
| ------------- | ----------------------------------------------------------------------------------------------- |
| **Lifespan**  | Valid for 20 minutes from creation.                                                             |
| **Reuse**     | Can be used multiple times during that window (e.g., account + subscription + one-time charge). |
| **Storage**   | Token lives only in the Recurly vault; if it expires it cannot be recovered.                    |
| **Transport** | Send to your server **over HTTPS only**—treat it like any auth credential.                      |

> **Tip:** If you receive `transaction_error.code = invalid_token`, request a fresh token from Recurly.js and retry.

***

## Endpoints that accept `token_id`

* **Purchase** — [Create Purchase](/developers/api/latest/index.html#operation/create_purchase)
* **Subscription** — [Create Subscription](/developers/api/latest/index.html#operation/create_subscription)
* **Account** — [Create](/developers/api/latest/index.html#operation/create_account) / [Update](/developers/api/latest/index.html#operation/update_account)
* **Billing Info** — [Update](/developers/api/latest/index.html#operation/update_billing_info)
* **Transaction** — [Create](/developers/api/latest/index.html#operation/list_account_transactions)

Attach the token like so:

```jsonc
"billing_info": {
  "token_id": "1d1e4f0447c2b7e6d2f6cbf5c4b2c9aa"
}
```

That’s it—Recurly swaps the token for the underlying card or bank details and completes the request while you stay out of PCI scope.