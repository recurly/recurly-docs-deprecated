---
title: 'Third-party checkout guide: Stripe Elements'
excerpt: A quick guide on how to use and implement third-party elements (Stripe).
deprecated: false
hidden: false
metadata:
  robots: index
---
# Overview

### Prerequisites & limitations

* An existing Stripe account with Stripe Elements enabled.
* A functional implementation of Stripe Payment Element (or Card Element) is required prior to following this guide.
* This guide focuses on Recurly’s V3 API. Refer to the [API Rerefence Hub](https://recurly.com/developers/api/) for more details.

***

# Third Party Checkout

### Stripe Elements Overview

This guide is intended to help you understand how to connect your Stripe Elements integration correctly to Recurly via the V3 API.

> 📘 Important:
>
> The integrator must have a functioning implementation of Stripe Elements in place in order to effectively use this guide. See Stripe Elements documentation and Recurly Recommendations below to get started.

For more information on the V3 API, see our [API Reference Hub](https://recurly.com/developers/api/).

### Supported Payment Methods (Q4 Support)

Recurly will be supporting Dynamic Payment Methods. To add payment methods to your integration and have them show up dynamically, request these payment methods in your Stripe Dashboard individually.

* **Cards**: Visa, MasterCard, Discover, Diners, JCB/I, Union Pay, American Express, Cartes Bancaires

* **Wallets**: Link Pay, Cash App Pay, Revolut, Klarna (Pay Now / Later, BNPL)

* **Direct Debit**: SEPA, ACH, BACS, BECS, iDeal

### Step 1: Build your Stripe Elements Integration

You will want to follow Stripe Documentation to build out an integration to the Stripe Payment Element. Documentation links are below:

* General Overview: [Stripe Payment Element](https://stripe.com/docs/payments/payment-element)
* Quickstart Guide: [Custom payment flow | Stripe Documentation](https://stripe.com/docs/payments/payment-element#custom-payment-flow)

If you decide to use the Card Element instead of the Payment Element, you will be limited to accepting only Card payments and Link, and excluding Wallets such as Apple and Google Pay. Stripe’s Payment Element, however, will support cards and Link, plus the additional wallets and alternative payment method options supported by Recurly. For a simpler and more flexible setup, we recommend choosing the Payment Element.

### Recommendations

* **Payment Element**: As noted above, the Payment Element option is best for simplicity and flexibility, but you can choose the Card Element option if you wish. Read more about the comparison at Stripe’s documentation below:

  * [Compare the Payment Element and Card Element | Stripe Documentation](https://stripe.com/docs/payments/payment-element#compare)

* **Best Practices**: Follow Stripe’s Best Practices guide, as well as Recurly-specific configuration requirements.

  * [Payment Element integration best practices | Stripe Documentation](https://stripe.com/docs/payments/payment-element#best-practices)

  * When using a payment method that requires mandates and legal terms, ensure you are choosing `auto` for payment options terms.

* **Credentials**: When using Elements with your Recurly Account, you must use your Stripe Account ID (acct\_xxxx) when configuring Elements and Recurly’s top-level PK key. If you use your own publishable key, the integration will not work properly. This is due to Recurly using Stripe Connect.

  * To retrieve your Stripe Account ID, you have two options:

    * **Option 1:** Log into your Stripe Gateway account directly, click on the ‘Settings’ gear icon, choose ‘Settings’ > ‘Business’. The account ID will be present in the UI for copying.

    * **Option 2:** Log into your Recurly account and navigate to ‘Configuration’ > ‘Payment Gateways’ and run a ‘Test Configuration’ against a Stripe Gateway instance. The associated account ID will be visible in the UI.

    * **Format:** `acct_xxxxxxxxxxxxx`

Use the ‘development mode’ key while your Recurly site is in ‘development mode’. Use the ‘production mode’ key for production transactions.

* **Recurly Development Mode Publishable Key**:\
  `pk_test_40DpPkZhqK69boNdC3ygyBgsCMGIzMq9rbRmeqBbD7ELUduU6gW4NcmKhvinbztWdiNVFfUfknl2OsCRDkFfVe7s7003wu2I6Mq`

* **Recurly Production Mode Publishable Key**: `pk_live_40DpPkZhqK69boNdC3ygyqOhhvwU83GPZTzPmQfuOe3LRwtHJaMYLH9ID5s7DvAi2bNpOASqoZSX1OfQhILfHb5Wu00aHgiDD2z`

* **Choosing your Mode**: Stripe’s Elements require a ‘mode’, and offers three options. Those options include `payment`, `setup`, and `subscription`. Recurly does not support the `subscription` mode.

It is important to understand your own checkout flow as you may use both setup and payment depending on your plan options or customer actions. First rule would be to always render the Stripe Element after you know if there is an amount to be charged or not.

Mode `payment` can be used any time a customer is signing up for a subscription where there is no trial, or is making a one-time purchase. This can also be used if they are updating their billing info and want to make a purchase at the same time. The best rule of thumb is to render the element after you know there will be a charge amount occurring (non-zero amount).

Mode `setup` should be used when a customer wishes to sign up for a free trial subscription, or is updating their billing information without making a purchase. This mode should also be used when creating a future-dated subscription, as we need to run a verification to retrieve reusable payment tokens for future usage. Again, the rule of thumb would be to use the `setup` mode when there is no expected amount for the immediate customer interaction.

### Configuring Capture Method

Stripe’s `captureMethod` Elements configuration parameter allows you to create a confirmation token that allows separate Authorization and Capture, or an all-in-one Purchase whether or not the intent is a subscription signup or a one-time transaction.

If you choose to set your Elements configuration to `captureMethod` of `manual`, you must use the /authorize endpoint when sending your customer initiated transaction with a confirmation token.

If your `captureMethod` configuration is set to `automatic`, you must use the purchase endpoint.

### Return URLs

Certain APMs require return URLs to be passed to Stripe within the confirmation token. To ensure customers who are choosing to checkout with a Return URL-dependent APM, ensure you are providing Stripe this detail before submitting the confirmation token to Recurly.

You can read more about Return URLs on Stripe’s Website on various pages. Recurly does not provide a return URL for usage with Stripe Elements.

### Choosing Where to Confirm Payment or Setup Intents

Recurly will confirm payment and setup intent on your behalf. You do not need to do this on your own.

* [Design an integration | Stripe Documentation](https://stripe.com/docs/payments/payment-element#design)

### External Payment Methods

Recurly does not support Stripe’s External Payment Methods option – if you choose to support these methods in your Elements integration, these transactions and payment methods will not be accepted for payments or subscriptions on the Recurly platform.

* [External payment methods | Stripe Documentation](https://stripe.com/docs/payments/payment-element#external-payment-methods)

If you would like to add external invoices from a self-hosted integration to Recurly, you may do so using the External Invoices feature.

* [Recurly Docs | External Invoices](https://docs.recurly.com/docs/using-app-management#/exports)

If you would like Recurly to support one of these External Payment Methods, please reach out to your Recurly Account manager and submit a feature request.

### Custom Billing Info Collection

Recurly suggests allowing Stripe to determine billing information collection dynamically, but you can modify this by customizing which billing information details you collect. See documentation below:

* [Control billing details collection | Stripe Documentation](https://stripe.com/docs/payments/payment-element#billing-details)

By default, all fields are set to `auto`. This balances minimizing customer friction and maintaining optimal authorization rates. If you choose to set your Elements integration to `never`, you will need to collect and pass those details via the Recurly API fields instead.

### Saving Payment Details

Since your checkout will be handling much of the buyer consent and recollection of information for known customers, it is imperative to follow Stripe’s and Recurly’s recommendations from both this page, and this guide.

When building your Payment Element, ensure you have also set `setup_future_usage` to `off_session` to ensure the payment method created can be used for future transactions.

* [Save and retrieve customer payment methods | Stripe Documentation](https://stripe.com/docs/payments/payment-element#save-payment-methods)
* Setup Future Usage: [Create an Elements object without an Intent](https://stripe.com/docs/payments/payment-element#setup-future-usage)

Key information from this guide includes:

* **Consent for Future Purchases**: Prompting buyers for consent before saving their payment details. Note, if customers do not check the box to store the payment method for future usage, the customer cannot use their payment method for additional purchases or subscriptions.

If this happens, `allow_redisplay` will appear in Confirmation Token retrievals as `limited`.

* When the confirmation token returns as ‘limited’, this consumer will not be able to use their billing info on file for additional subscriptions or one-time purchases. Expect declines for attempts using a Stripe token in this state.

### Migrating from Stripe Payment Methods to Confirmation Tokens

If your current integration with Stripe uses Payment Methods, see Stripe’s documentation for migrating to Confirmation Tokens here:

* [Migrate to Confirmation Tokens | Stripe Documentation](https://stripe.com/docs/payments/payment-element#migrate)

### Stripe Messaging

* [Payment Method Messaging Element | Stripe Documentation](https://stripe.com/docs/payments/payment-element#messaging)

A consolidated list of parameters and our recommendations are below. We have not covered all Stripe documented Element options configuration available as, in those cases, they can be set based on your preferences.

| Elements Option                                      | Value                  | Description                                                                                                                                                                                                                                                                         |
| ---------------------------------------------------- | ---------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| mode                                                 | payment or setup       | payment when the transaction includes an initial dollar amount, such as when signing up for a subscription with no free trial, or a one-time purchase. setup when the transaction does not include a dollar amount, such as a free trial. subscription is not supported by Recurly. |
| currency                                             | ISO-standard, 3 Letter | Ensure your transaction payload to Recurly matches the currency configured for the given c\_token value.                                                                                                                                                                            |
| amount                                               | Up to eight digits     | Only sent when the mode is payment                                                                                                                                                                                                                                                  |
| setupFutureUsage                                     | off\_session           | Ensure this is set to off\_session so that Stripe payment methods resulting from the given confirmation token can be used for recurring purposes. Setting this param to on\_session will cause recurring transactions to fail.                                                      |
| captureMethod                                        | manual or automatic    | manual when you wish to run an /authorize (separate auth and capture) flow. automatic when you wish to run an /purchase or /subscription (sale, all in one transaction) flow.                                                                                                       |
| paymentMethodTypes                                   | none                   | Omit this value. Recurly handles payment methods using Stripe’s automatic payment methods. Ensure you have only those payment methods you wish to enable set up in your Stripe dashboard.                                                                                           |
| paymentMethodConfiguration                           | none                   | Omit this value. Recurly handles payment methods using Stripe’s automatic payment methods. Ensure you have only those payment methods you wish to enable set up in your Stripe dashboard.                                                                                           |
| paymentMethodOptions.card.require\_cvc\_recollection | boolean                | Set this to true if you wish to require CVV collection from known customers. Recurly supports passing cvv in these cases.                                                                                                                                                           |

### Step 2: Retrieve the Confirmation Token Details (Optional)

If you need to calculate taxes or perform other pre-transaction activities, and require the customer details to do so, you can query the Confirmation Token ID you receive after a successful Elements interaction.

The confirmation Token contains information about the payment method selected during the checkout process, any billing and shipping information collected among other important details such as the `setup_future_usage` parameter. If this transaction is meant to offer recurring capabilities or if you need to reattempt a declined transaction, it should be set to `off_session`.

* [Confirmation Token Documentation: The Confirmation Token object | Stripe API Reference](https://stripe.com/docs/api/confirmation_tokens/object)
* [Retrieve a ConfirmationToken | Stripe API Reference](https://stripe.com/docs/api/confirmation_tokens/retrieve)

For tax calculation purposes, relevant information will be provided in this object:

```json
"billing_details": {
  "address": {
    "city": "Hyde Park",
    "country": "US",
    "line1": "50 Sprague St",
    "line2": "",
    "postal_code": "02136",
    "state": "MA"
  },
  "email": "jennyrosen@stripe.com",
  "name": "Jenny Rosen",
  "phone": null
},
```

Shipping details, if collected, are also in this response as below:

```json
"shipping": {
  "address": {
    "city": "Hyde Park",
    "country": "US",
    "line1": "50 Sprague St",
    "line2": "",
    "postal_code": "02136",
    "state": "MA"
  },
  "name": "Jenny Rosen",
  "phone": null
}
```

> 📘 Important:
>
> A Stripe Confirmation Token will be valid for only **12 hours**. You should retrieve and send the token to Recurly while the customer is still in session. Recurly is unable to make use of Stripe Confirmation Tokens which have expired.

### Step 3: Create a Purchase Request

To make a purchase using the Stripe Confirmation Token, you will provide it in a Recurly V3 API purchase request. In the example below, one subscription is generated using the plan code created in our [Quickstart Guide](https://docs.recurly.com/v1.1/docs/quick-start-guide#/).

The following parameters are used specifically for this use-case.

| Parameter                    | Description                                                                                                                                             |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| payment\_gateway\_references | Array of Payment Gateway References, each a reference to a third-party gateway object of varying types. Child of billing\_info.                         |
| token                        | An identifier of the reference. Property of a Payment Gateway Reference.                                                                                |
| reference\_type              | The reference type of the token. May be one of: stripe\_confirmation\_token, stripe\_customer, stripe\_payment\_method                                  |
|                              | **Note:** confirmation\_token cannot be sent with customer or payment\_method tokens. Stripe customer and payment\_method tokens must be sent together. |

An example request body to POST /purchases:

```json
{
  "currency": "USD",
  "account": {
    "code": "my-new-account",
    "billing_info": {
      "payment_gateway_references": [
        {
          "token": "my_confirmation_token_id",
          "reference_type": "stripe_confirmation_token"
        }
      ]
    },
    "subscriptions": [
      {
        "plan_code": "coffee-monthly"
      }
    ]
  }
}
```

Many additional options are available to you. See the [Create Purchase](https://recurly.com/developers/api/latest/index.html#operation/create_purchase) reference to learn more.

#### Redirect Method APMs

Certain APMs require displaying a QR code or other redirect to the consumer so they can authenticate in the Cash App application on their mobile device. Recurly utilizes our 3-D Secure exo-system to process Cash App transactions. When you provide a Stripe `ctoken` value in your API request, Recurly will respond with a `three_d_secure_action_token`

This token can be provided to Recurly.js to unpack and direct your consumer to complete their Cash App payment.

After your consumer processes their payment with Cash App, Recurly.js will produce a `three_d_secure_action_result_token` which you may provide in a retry of your initial purchase call. See `Step 4` for more details on parsing the purchase response.

See [Recurly.js Action Token documentation](https://recurly.com/developers/reference/recurly-js/#3d-secure) at our Developer Hub.

> 📘 Important:
>
> **Related APMs:** Cash App and Revolut
>
> **Please Note:** For Revolut, you must submit the action token to Recurly.js twice, to receive a result token.

#### Direct Debit and Country Restricted Payment Methods

Certain payment methods require the Stripe business account holder to be located in the respective country of support. For example, you will not be able to enable BACS or Revolut without a UK domiciled business, and BECS cannot be enabled outside of Australia.

**Related Payment Methods:** BACS (UK), BECS (AU), Revolut (UK)

**Direct Debit Support Note:** BACS do not support setup intents through the Payment Element, and therefore <u>cannot support Billing Information updates or Free Trial subscriptions using Stripe Elements where payment details are required</u>. To support Billing Information updates, subscriptions will need to be cancelled and set up again rather than using billing update features on Recurly. To support Trials using BACS, you may use trials that do not require payment data. Upon conversion, the customer will need to go through the Elements flow.

### Step 4: Process the Purchase Response

If the purchase was not successful, you’ll receive an error response indicating the type of error that was encountered. Errors could occur due to a bad request (missing or invalid parameters), or other reasons such as payment gateway failures.

If the purchase is successful, an `InvoiceCollection` will be returned as the response type. This object consists of any charge or credit invoices created during the purchase.

### Step 5: Verify and Finish

If the purchase was successful, you should now be able to access all associated objects that were created as a result. You can verify through the API or the admin console.

### Next Steps

Now that you know how to create new [accounts](https://app.recurly.com/go/accounts), [subscriptions](https://app.recurly.com/go/subscriptions) and one-time payments, take a look at the [Subscription Management](https://recurly.com/developers/guides/manage-subscription.html) guide to learn more about how to manage the subscription changes after the initial purchase.

### Using Recurly.js and Stripe Elements

In many cases, you may wish to retain your existing Recurly.js integration and supplement with Stripe Elements to allow users access to more alternative payment options. This is possible, though will be specific to your business needs and design of your unique checkout experience.

Card payments, often the leading payment method, can be accepted with Recurly.js to allow you to leverage several gateways, 3-D Secure solutions, and the multitude of fraud prevention products we offer.

If you have been or plan to use the Recurly.js pricing components, those may operate alongside Stripe Elements to provide real-time purchase price, tax estimates, discount calculations and more.

Alongside these Recurly.js capabilities, Stripe Elements may be used solely to implement payment methods not provided by Recurly.js for Stripe. We recommend this approach as a manner of minimally impacting any existing Recurly.js integration.\\