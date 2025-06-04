---
title: 'Third-party checkout guide: Adyen Web Components'
excerpt: >-
  A step-by-step reference for wiring up Adyen Web Components to Recurly via the
  V3 API and Recurly.js, enabling you to accept cards, wallets, and other
  payment methods with tokenization and vaulting.
deprecated: false
hidden: false
metadata:
  robots: index
---
# Overview

This guide walks you through how to connect your Adyen Web Components integration correctly to Recurly via the V3 API and Recurly.js. You’ll learn how to configure Adyen’s Web Components, tokenize payments with Recurly.js, and make purchase requests against the Recurly API.

### Prerequisites & limitations

* A working Adyen Components integration using the **Advanced Flow** (Cards, Cash App Pay, Google Pay, Apple Pay).
* Recurly.js loaded on your page and initialized per our [Recurly.js documentation](/developers/reference/recurly-js).
* Access to your Recurly V3 API credentials and a Recurly site configured to accept vault‑enabled payments.
* **Not supported**: Adyen Sessions flow or non‑Web Components.
* **Supported payment methods**:

  * **Cards**: Visa, MasterCard, Discover, Diners, JCB/I, Union Pay, American Express, Cartes Bancaires
  * **Wallets**: Apple Pay, Google Pay, Cash App Pay

***

## Step 1: Build your Adyen components + Recurly.js integration

Follow Adyen’s **Advanced Flow** docs to render Web Components for each method:

* Cards: [Card Web Component | Adyen Docs](https://docs.adyen.com/payment-methods/cards/web-component/?tab=advanced-requirements_2)
* Cash App Pay: [Cash App Pay Component | Adyen Docs](https://docs.adyen.com/payment-methods/cash-app-pay/web-component/?tab=advanced-requirements_2)
* Google Pay: [Google Pay Component | Adyen Docs](https://docs.adyen.com/payment-methods/google-pay/web-component/)

  * Express Checkout: [Google Pay express checkout | Adyen Docs](https://docs.adyen.com/payment-methods/google-pay/web-component/express-checkout/)
* Apple Pay: [Apple Pay Component | Adyen Docs](https://docs.adyen.com/payment-methods/apple-pay/web-component/?tab=advanced-requirements_2)

  * Express Checkout: [Apple Pay express checkout | Adyen Docs](https://docs.adyen.com/payment-methods/apple-pay/web-component/express-checkout/)

Before rendering, fetch your supported methods via Adyen’s API and pass the `paymentMethodsResponse` into the Components configuration. Only include methods that your Recurly site supports.

```json
"paymentMethodsResponse": {
  "paymentMethods": [
    {
      "brands": [
        "amex",
        "cup",
        "diners",
        "discover",
        "mc",
        "visa"
      ],
      "name": "Cards",
      "type": "scheme"
    }
  ]
}
```

***

## Step 2: Tokenize Adyen components with Recurly.js

Use an `onSubmit` handler in your Adyen checkout to generate a Recurly token from the component state. Send this token to your server to complete the purchase via Recurly’s API.

```javascript
const onSubmit = async (state, component, actions) => {
  try {
    let payload = {
      type: 'adyen_component_state',
      adyen_component_state_context: state
    };

    recurly.token(payload, (error, token) => {
      if (error) {
        actions.reject();
        return;
      }
      // Send token.nonce to your backend to create a purchase via Recurly API
      actions.resolve();
    });
  } catch (error) {
    actions.reject();
  }
};

const adyenCheckout = await AdyenWeb.AdyenCheckout({
  onSubmit
});
```

***

## Step 3: Configure Adyen component best practices

Ensure the following options are set for reliable vaulting and renewals:

| Option                    | Recommended Setting | Notes                                                                                                     |
| :------------------------ | :------------------ | :-------------------------------------------------------------------------------------------------------- |
| `storePaymentMethod`      | `true`              | Required for tokenization; if omitted or set to `false`, no vault token is issued and renewals will fail. |
| `enableStoreDetails`      | omit                | Avoid using; allows user opt‑out and can break renewals if declined.                                      |
| `hideCVC`                 | conditional boolean | Set to `true` unless your Adyen setup explicitly supports CVC bypass on return customers.                 |
| `maskSecurityCode`        | `true`              | Masking the CVV field enhances security and user trust.                                                   |
| `hasHolderName`           | omit                | Not supported—use Recurly.js name elements.                                                               |
| `billingAddressRequired`  | omit                | Not supported—use Recurly.js address elements if you need AVS.                                            |
| `addressSearchDebounceMs` | omit                | Not supported—Recurly does not process Adyen address search elements.                                     |
| `installmentOptions`      | omit                | Recurly does not support Adyen installment features.                                                      |
| `showInstallmentAmounts`  | omit                | Recurly does not display installment breakdowns; handle in your own UI if needed.                         |

For full Adyen advanced flow guidance, see:

* [Advanced flow integration guide | Adyen Docs](https://docs.adyen.com/online-payments/build-your-integration/advanced-flow/?platform=Web\&integration=Components\&version=6.5.1)

***

## Step 4: Create a purchase via the Recurly V3 API

Once you have a valid component token, invoke the V3 API’s purchase endpoint. For example, to subscribe a plan:

```http
POST https://v3.recurly.com/purchases
Content-Type: application/json
Authorization: Bearer YOUR_API_KEY

{
  "currency": "USD",
  "subscriptions": [
    { "plan_code": "gold-monthly" }
  ],
  "account": { "account_code": "user123" },
  "gateway": "adyen",
  "payment": {
    "token_id": "TOKEN_FROM_RECURLY_JS",
    "return_url": "https://your.site/confirmation"
  }
}
```

***

## Step 5: Handle the purchase response

– On success, Recurly returns an `InvoiceCollection` containing any charge or credit invoices created.\
– On error, inspect the response code and message for validation or gateway issues, and surface them to the user.

***

## Next steps

After completing an initial purchase, explore our Subscription Management guide to learn how to update, cancel, or migrate subscriptions:

* [Subscription Management](https://docs.recurly.com/v1.1/docs/managing-subscription-methods-guides#/)