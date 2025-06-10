---
title: Boleto, iDEAL, Sofort, and CashApp
excerpt: >-
  Implement Boleto, iDEAL, Sofort, and Cash App with a single Recurly.js
  integration that renders the Adyen payment component, handles tokenization,
  and returns a secure payment-method token for your server-side purchase call.
deprecated: false
hidden: false
metadata:
  robots: index
---
`recurly.AlternativePaymentMethods` is a convenience wrapper around Adyen’s JavaScript component.\
With one configuration object you can:

* **Render** a localized payment-method UI (Boleto, iDEAL, Sofort, or Cash App) inside any container.
* **Tokenize** the shopper’s details—no sensitive information hits your server.
* **Emit** a Recurly token that you send to any v3 endpoint that accepts `billing_info`.
* **Hand off / handle actions** (iDEAL & Sofort redirects) or skip them entirely (Cash App).

### Prerequisites and limitations

* Your Recurly site must be connected to **Adyen** and the payment-method(s) you intend to offer must be enabled in your Adyen merchant account.
* For **iDEAL** and **Sofort** you **must** provide a `returnURL`.
* Cash App does **not** return an `action_result`; you can ignore `paymentMethod.handleAction`.

### Configuration

To start with any of these payment methods, call `recurly.AlternativePaymentMethods`.

For example:

```js
const paymentMethod = recurly.AlternativePaymentMethods({
  allowedPaymentMethods: [
    "boleto"
  ],
  blockedPaymentMethods: [],
  containerSelector: "#payment-methods-container",
  amount: 10,
  currency: "BRL",
  countryCode: "BR",
  locale: "en-US",
  channel: "Web",
  adyen: {
    publicKey: 'adyen_public_key',
    env: "test",
    showPayButton: false,
    componentConfig: {}
  },
  returnURL: 'https://return-url.com/success',
});
```

See reference for details. The various payment methods may be configured as follows:

### Boleto

| Param                 | Type     | Description                      |
| :-------------------- | :------- | :------------------------------- |
| allowedPaymentMethods | `Array`  | Array needs to contain `boleto`. |
| currency              | `String` | String needs to be `BRL`.        |
| countryCode           | `String` | String needs to be `BR`          |

### iDEAL

| Param                 | Type     | Description                           |
| :-------------------- | :------- | :------------------------------------ |
| allowedPaymentMethods | `Array`  | Array needs to contain `ideal`.       |
| currency              | `String` | String needs to be `EUR`.             |
| countryCode           | `String` | String needs to be `NL`               |
| returnURL             | `String` | The return url is required for iDEAL. |

### Sofort

| Param                 | Type     | Description                                                                        |
| :-------------------- | :------- | :--------------------------------------------------------------------------------- |
| allowedPaymentMethods | `Array`  | Array needs to contain `sofort`.                                                   |
| currency              | `String` | String should have one of the following currencies: `EUR`, `CHF`                   |
| countryCode           | `String` | String should have one of the following values: `AT`, `BE`, `DE`, `ES`, `CH`, `NL` |
| returnURL             | `String` | The return url is required for Sofort.                                             |

### CashApp

| Param                 | Type     | Description                       |
| :-------------------- | :------- | :-------------------------------- |
| allowedPaymentMethods | `Array`  | Array needs to contain `cashapp`. |
| currency              | `String` | String needs to be `USD`.         |
| countryCode           | `String` | String needs to be: `US`.         |

### Rendering the Payment Methods

After configuring your recurly instance, call `paymentMethod.start()` to show each configured Payment Method(s) within\
the target element.

Call `paymentMethod.destroy()` to safely remove the rendered payment UI from the document. This is necessary if you\
wish to re-render the component.

### Generating a token

After filling and confirming the data, `paymentMethod.submit()` should be called so that the token event can be\
dispatched. You may optionally provide a specific customer billing address to this call.

```js
paymentMethod.submit();

// You may wish to provide a specific customer billing address when calling `submit`.
paymentMethod.submit({
  billingAddress: {
    first_name: 'Ben',
    last_name: 'du Monde',
    address1: '1313 Main St.',
    address2: 'Unit 1',
    city: 'Hope',
    state: 'WA',
    postalCode: '98552',
    country: 'US'
  }
});

```

After submit is called, the token will be emitted in the `token` event.

```js
paymentMethod.on('token', function (token) {
  // token.id
});
```

### Making a purchase

Having the token, a request must be sent to the server, which internally will call the Recurly API to make the purchase.\
The request must contain the fields from the previously filled in the element container. The response must then be
handled to retrieve the `action_result` and pass it back to the browser. With the action result,
`paymentMethod.handleAction(action_result)` can be called.

<div class="alert alert--warning" markdown="1">
  **Using Cash App?**

  Cash App does not use an `action_result` so `paymentMethod.handleAction` can be ignored.
</div>

```js
const form = document.querySelector('form')

fetch(form.action, {
  method: form.method,
  body: new FormData(form),
})
  .then(response => response.json())
  .then(data => {
    console.log({ data })
    if (data.error) {
      alert(data.error);
    } else if (data.action_result) {
      paymentMethod.handleAction(data.action_result)
    }
  })
  .catch(err => {
    console.error(err);
    notifyErrors([err]);
    });
```

### Reference

#### <span class="heading-tag heading-tag--fn">fn</span> recurly.AlternativePaymentMethods

##### Arguments

| Param                           | Type      | Description                                                                  |
| :------------------------------ | :-------- | :--------------------------------------------------------------------------- |
| options                         | `Object`  |                                                                              |
| options.allowedPaymentMethods   | `Array`   | An array containing the desired payment method.                              |
| options.blockedPaymentMethods   | `Array`   | An array containing the payment methods that will be blocked.                |
| options.containerSelector       | `String`  | String containing the CSS selector of the container.                         |
| options.amount                  | `Integer` | The amount to be paid.                                                       |
| options.currency                | `String`  | The currency for the transaction.                                            |
| options.locale                  | `String`  | The locale for the transaction.                                              |
| options.channel                 | `String`  | The channel.                                                                 |
| options.adyen                   | `Object`  | An object containing adyen gateway configuration.                            |
| options.adyen.publicKey         | `String`  | The public key.                                                              |
| options.adyen.env               | `String`  | The environment.                                                             |
| options.adyen.showPayButton     | `Boolean` | Whether to show or not the pay button.                                       |
| options.componentConfig         | `Object`  | An object with the component configuration.                                  |
| options.returnURL               | `String`  | Return URL (needed only for iDEAL and Sofort).                               |
| options.customer                | `Object`  | An object containing additional customer details.                            |
| options.customer.billingAddress | `Object`  | Customer billing address. See [Billing Fields](#billing-fields) for options. |

##### Returns

A new `AlternativePaymentMethods` instance

#### <span class="heading-tag heading-tag--fn">fn</span> alternativePaymentMethods.start

##### Arguments

None.

##### Returns

Nothing.

#### <span class="heading-tag heading-tag--fn">fn</span> alternativePaymentMethods.submit

##### Arguments

| Param                     | Type     | Description                                                                  |
| :------------------------ | :------- | :--------------------------------------------------------------------------- |
| \[options]                | `Object` |                                                                              |
| \[options.billingAddress] | `Object` | Customer billing address. See [Billing Fields](#billing-fields) for options. |

##### Returns

Nothing.

#### <span class="heading-tag heading-tag--event">events</span> Emits

##### `error`

This event is emitted when any error is encountered, whether during setup of the payment method flow, or during customer\
interaction with the payment method interface.

##### Payload

| Param | Type           | Description                                  |
| :---- | :------------- | :------------------------------------------- |
| error | `RecurlyError` | An error describing the issue that occurred. |

##### `token`

This event is fired when the customer has completed the payment method flow. Recurly has received the payment details,\
and generated this token to be used in our API.

##### Payload

| Param      | Type     | Description                            |
| :--------- | :------- | :------------------------------------- |
| token      | `Object` |                                        |
| token.type | `String` | 'payment\_method'                      |
| token.id   | `String` | Token identifier to be sent to the API |

***