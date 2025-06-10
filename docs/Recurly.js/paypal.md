---
title: PayPal
excerpt: >-
  Add a “Pay with PayPal” button in minutes—Recurly.js opens the PayPal
  checkout, returns a one-time token, and lets you charge through PayPal
  Business, PayPal Complete, or Braintree.
deprecated: false
hidden: false
metadata:
  robots: index
---
`recurly.PayPal` wraps PayPal’s pop-up flow for the web. When a shopper authorizes the payment:

1. A PayPal window opens and collects approval.
2. Recurly receives the PayPal billing-agreement details and gives your browser a short-lived token.
3. Your front end sends that token to your server, where any v3 endpoint that accepts `billing_info` can create the charge.

The same JavaScript integrates with **PayPal Business**, **PayPal Complete**, or **Braintree**—just pass the option that matches your gateway.

***

### Prerequisites and limitations

* Your site must have **at least one** of the following gateways active in Recurly: **PayPal Business, PayPal Complete, or Braintree** with PayPal enabled.
* The `start()` call must occur inside a **user-initiated** event handler (`click`, `touchend`, etc.).
* PayPal opens a pop-up; if your site sets [**Cross-Origin Opener Policy**](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cross-Origin-Opener-Policy) you must allow cross-origin communication.

# Key details

First, place a button on your page specifically for checking out with PayPal.

```html
<button>Checkout with PayPal</button>
```

Next, create a new `recurly.PayPal` instance

```javascript
const paypal = recurly.PayPal({
  display: { displayName: ' My product ' }
});
```

**If you're processing PayPal transactions with Braintree**, you'll pass a client authorization during instantiation:

```javascript
const paypal = recurly.PayPal({
  braintree: { clientAuthorization: MY_CLIENT_AUTHORIZATION }
});
```

**If you're processing PayPal transactions with PayPal Complete**, you'll need to pass a payPalComplete flag:

```javascript
const paypal = recurly.PayPal({
  payPalComplete: true
});
```

Your instance must then be setup to handle error scenarios and start the checkout flow.

```javascript
paypal.on('error', function (err) {
  // err.code
  // err.message
  // [err.cause] if there is an embedded error
});
```

Next we must bind a listener to a user action on the button and have it trigger the `start` function on your `recurly.PayPal` instance. This will open the PayPal checkout flow.

```javascript
document.querySelector('#paypal-button').addEventListener('click', function () {
  paypal.start();
});
```

> **Note**:  The `start` function must be called within a user-initiated event like 'click' or 'touchend'.

Finally, add a function to receive the token once your customer completes the checkout flow. At this point you will send the token id to your server to be used in the Recurly API to create a billing info for an account.

For PayPal Business and Braintree the token also includes the `email` and `payer_id` of the billing agreement. For PayPal Complete the token contains only a `id` to be used in the Recurly API.

```javascript
paypal.on('token', function (token) {
  // token.id
  // token.email
  // token.payer_id
});
```

> **Note**: PayPal utilizes a popover window to facilitate its customer payment flow. If you set a [Cross-Origin Opener Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cross-Origin-Opener-Policy), it must permit cross-origin communication.

### Reference

#### <span class="heading-tag heading-tag--fn">fn</span> recurly.PayPal

##### Arguments

| Param                                 | Type      | Description                                                                                     |
| :------------------------------------ | :-------- | :---------------------------------------------------------------------------------------------- |
| options                               | `Object`  | Optional.                                                                                       |
| options.braintree                     | `Object`  | Optional. Braintree configuration.                                                              |
| options.braintree.clientAuthorization | `String`  | If using Braintree to process PayPal transactions, provide your client authorization code here. |
| options.payPalComplete                | `Boolean` | Optional. If using PayPal Complete to process PayPal transactions, pass true here.              |

##### Returns

A new `PayPal` instance

#### <span class="heading-tag heading-tag--fn">fn</span> payPal.start

##### Arguments

| Param               | Type     | Description                                                                                    |
| :------------------ | :------- | :--------------------------------------------------------------------------------------------- |
| options             | `Object` | Optional.                                                                                      |
| options.description | `String` | Optional. In legacy PayPal flows, this description will be displayed during the checkout flow. |

##### Returns

Nothing.

#### <span class="heading-tag heading-tag--event">events</span> Emits

##### `error`

This event is emitted when any error is encountered, whether during setup of the PayPal flow, or during the checkout process. It will be useful to display errors to your customer if a problem occurs during PayPal checkout.

##### Payload

| Param | Type           | Description                                  |
| :---- | :------------- | :------------------------------------------- |
| error | `RecurlyError` | An error describing the issue that occurred. |

##### `cancel`

This event is emitted when the customer has canceled the PayPal checkout flow before completion. You may wish to reset parts of your checkout experience if this occurs.

##### Payload

None.

##### `token`

This event is fired when the customer has completed the PayPal checkout flow. Recurly has received the payment details, and generated this token to be used in our API.

##### Payload

| Param           | Type     | Description                                           |
| :-------------- | :------- | :---------------------------------------------------- |
| token           | `Object` |                                                       |
| token.type      | `String` | 'paypal'                                              |
| token.id        | `String` | Token identifier to be sent to the API                |
| token.email     | `String` | The email of the customer.                            |
| token.payer\_id | `String` | The unique identifier of the customer PayPal account. |

***