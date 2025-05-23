---
title: 'Third-party checkout guide: Adyen components'
deprecated: false
hidden: true
metadata:
  robots: index
---
# Third Party Checkout: Adyen Components

## Overview

This guide is intended to help you understand how to connect your Adyen Components integration correctly to Recurly via the V3 API and Recurly.js.

The integrator must have a functioning implementation of Adyen Components and Recurly.js in place in order to\
effectively use this guide.

For more information on the V3 API, see our [API v3 documentation](/developers/api/) and [Recurly.js documentation](/developers/reference/recurly-js).

### Supported Payment Methods

* **Cards:** Visa, MasterCard, Discover, Diners, JCB/I, Union Pay, American Express, Cartes Bancaires
* **Wallets:** Apple Pay, Google Pay, Cash App Pay

## Step 1: Build your Adyen Components + Recurly.js Integration

We recommend following Adyen Documentation to build out an integration to the Adyen **Web** Components using the Advanced Flow. Recurly does not support the Sessions flow or non-Web Components.

* Cards: [Card Web Component | Adyen Docs](https://docs.adyen.com/payment-methods/cards/web-component/?tab=advanced-requirements_2)
* Cash App: [Cash App Pay Component | Adyen Docs](https://docs.adyen.com/payment-methods/cash-app-pay/web-component/?tab=advanced-requirements_2)
* Google Pay: [Google Pay Component | Adyen Docs](https://docs.adyen.com/payment-methods/google-pay/web-component/)
  * Express Checkout: [Google Pay express checkout | Adyen Docs](https://docs.adyen.com/payment-methods/google-pay/web-component/express-checkout/)
* Apple Pay: [Apple Pay Component | Adyen Docs](https://docs.adyen.com/payment-methods/apple-pay/web-component/?tab=advanced-requirements_2)
  * Express Checkout: [Apple Pay express checkout | Adyen Docs](https://docs.adyen.com/payment-methods/apple-pay/web-component/express-checkout/)

Adyen Components will require querying on available payment methods before configuring the Component. Ensure you are populating the `paymentMethodsResponse.`The payment methods included in the Components configuration will inform which payment methods are offered to the consumers, giving you access to modify what is available to the user. Do not add payment methods that Recurly does not support or your account does not have available.

* Documentation: [Adyen API Explorer](https://docs.adyen.com/api-explorer/Checkout/latest/post/paymentMethods)

**Example**:

The below will only allow checkout via Card payment. You can omit cards, and use Adyen Configuration with only alternative payment methods if you wish to accept cards with Recurly Elements.

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
`}
```

### Tokenizing Adyen Components with Recurly.js

Adyen Components can be tokenized with Recurly.js. The resulting token can be used to create  a purchase with Recurly. In the following example, we use an onSubmit handler to create a token from the Adyen Component state.

```javascript
const onSubmit = async (state, component, actions) => {
  try {
    /*
     * Additional billing information may be provided in the payload:
     *
     * company: string
     * first_name: string
     * last_name: string
     * address1: string
     * address2: string
     * city: string
     * state: string
     * postal_code: string
     * country: string
     * tax_identifier: string
     * tax_identifier_type: string
     */
    let payload = {
      type: 'adyen_component_state',
      adyen_component_state_context: state
    };

    recurly.token(payload, (error, token) => {
      if (error) {
        // handle the error
        actions.reject();
        return;
      }

      // At this point, send the token to your server to be used with Recurly's APIs

      actions.resolve();
    });
  } catch (error) {
    // handle the error
    actions.reject();
  }
};

const adyenCheckout = await AdyenWeb.AdyenCheckout({
  onSubmit
});
```

### Adyen Component Configuration Recommendations

Best Practices: the following settings in your Adyen Components integration will work best with Recurly payments.

* `storePaymentMethod``:` This value must be set to  `true` in your Component configuration. Allowing the customer to\
  opt-out or setting this value to ‘false’ will disable tokenization and payment method storage behaviors for Renewals
  and return-customer behaviors.

* Similarly, using `enableStoreDetails` instead of `storePaymentMethod` will allow the customer to opt-out of payment\
  method storage and will cause renewal and return customer failures to occur.

A consolidated list of parameters and our recommendations are below. We have not covered all Adyen documented Web\
Component options configuration available as, in those cases, they can be set based on your preferences.

| Web Components Option   | Value                 | Description                                                                                                                                                           |
| :---------------------- | :-------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| storePaymentMethod      | true                  | Ensure this is set to ‘true’ to avoid renewal failures.                                                                                                               |
| enableStoreDetails      | omit                  |                                                                                                                                                                       |
| hideCVC                 | conditional boolean   | Only set to ‘false’ if your Adyen account is set up to support bypassing the CVV code for return customers. Otherwise, set to ‘true’.                                 |
| maskSecurityCode        | conditional boolean   | Recommended to mask the CVV as the consumer enters their card security code.                                                                                          |
| hasHolderName           | omit - not supported. | Ensure you are using Recurly.js name elements to pass the name properly.                                                                                              |
| billingAddressRequired  | omit - not supported. | If your Recurly site settings require all billing info details, or you wish to support AVS rejection logic, ensure you are using Recurly.js billing address elements. |
| addressSearchDebounceMs | omit - not supported. | Recurly does not support Adyen address elements in order to support the search feature.                                                                               |
| installmentOptions      | Omit entirely.        | Recurly does not require installment notes.                                                                                                                           |
| showInstallmentAmounts  | Omit entirely.        | Recurly does not require installment notes.                                                                                                                           |

When building your Adyen Components flow, ensure you are following the proper guidance on Adyen’s website. We align\
closely with their  ‘Advanced Flow’ implementation guidance.

Read more on Adyen’s Advanced Components flow:

* [Advanced flow integration guide | Adyen Docs](https://docs.adyen.com/online-payments/build-your-integration/advanced-flow/?platform=Web\&integration=Components\&version=6.5.1)

### External Payment Methods

If you would like to add external invoices from a self-hosted integration to Recurly, you may do so using the External\
Invoices feature.

* [Recurly Docs | External Invoices](https://docs.recurly.com/docs/external-invoices)

### Custom Billing Info Collection

Recurly requires usage of Recurly.js billing name and address fields when collecting consumer name and address\
information. Using Adyen Components for Name or Address data will result in the data being omitted when processing a
transaction. Depending on your Recurly site settings, this absence can cause errors, or authorization failures.

### Saving Payment Details

Since your checkout will be handling much of the buyer consent and recollection of information for known customers, it\
is imperative to follow Adyen’s and Recurly’s recommendations from both this page, and this guide.

When building your Web Components, ensure you have also created a payment method that can be used for future transactions.

* [Store card details | Adyen Docs](https://docs.adyen.com/payment-methods/cards/web-component/?tab=store-card-details-payment-methods_2#store-card-details)

**Key information from this guide includes:**

* Consent for Future Purchases: Prompting buyers for consent before saving their payment details. Note, if customers do not check the box to store the payment method for future usage, the customer cannot use their payment method for additional purchases or subscriptions. Triggering conditional storage refers to `enableStoreDetails`, which we do not recommend using.

### Co-badged guidance

If you choose to [show debit and credit cards separately](https://docs.adyen.com/payment-methods/cards/web-component/?tab=advanced_flow_1_2#show-debit-credit-separately).

If you are accepting co-badged cards, such as Cartes Bancaires, ensure you are following all applicable regional\
requirements for co-badged cards in your UI and Card Web Component handling. In short, the regulations can be whittled down to two factors:

* Use ‘Cards’ instead of ‘Credit Card’ and ‘Ensure the consumer has the ability to select their desired network choice.
* Adyen Documentation: [Co-badged cards compliance | Adyen Docs](https://docs.adyen.com/online-payments/co-badged-cards-compliance/)

Recurly already supports co-badge compliance, so renewals will honor the consumer’s choice for renewals by default. To\
avoid complexity, it is recommended to avoid separating Debit and Credit in the UI.

## Step 3: Create a purchase request

To make a purchase using the Recurly.js Adyen State Token ID and Return URL, if applicable, you will provide it in a\
Recurly V3 API purchase request. In the example below, one subscription is generated using the plan code created in our
[Quickstart Guide](/developers/guides/quickstart.html).

Documentation: [API Reference | Recurly Developer Hub](/developers/api)

## Step 4: Process the purchase response

If the purchase was not successful, you’ll receive an error response indicating the type of error that was encountered.

Errors could occur due to a bad request (missing or invalid parameters), or other reasons such as payment gateway\
failures.

If the purchase is successful, an InvoiceCollection will be returned as the response type. This object consists of any\
charge or credit invoices created during the purchase.

## Step 5: Verify and finish

If the purchase was successful, you should now be able to access all associated objects that were created as a result. You can verify through the API or the admin console.

## Next Steps

Now that you know how to create new [accounts](https://app.recurly.com/go/accounts), [subscriptions](https://app.recurly.com/go/subscriptions) and one-time payments, take a look at the\
[Subscription Management](/developers/guides/manage-subscription.html) guide to learn more about how to manage the subscription changes after the initial purchase.