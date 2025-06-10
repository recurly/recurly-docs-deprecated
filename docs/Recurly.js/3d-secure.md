---
title: 3D Secure
excerpt: >-
  Add PSD-2 compliant 3-D Secure (3DS 2.x) flows to your Recurly.js checkout so
  card-holders can complete issuer-mandated authentication without leaving your
  page.
deprecated: false
hidden: false
metadata:
  robots: index
---
`recurly.Risk().ThreeDSecure()` lets you trigger and complete Strong Customer Authentication (SCA) in-browser. Pass the `three_d_secure_action_token_id` (or proactive token) you received from the Recurly API, attach the component to an element on your page, and listen for either a `token` (success) or `error` event. The library handles device-fingerprinting, challenge display, and returns a `three_d_secure_action_result` token you can use in any subsequent API call (purchase, subscription, billing-info update, etc.).

## Prerequisites & limitations

* Your site must use a gateway that Recurly supports for 3DS 2.x (e.g., Braintree, Cybersource, Worldpay, Adyen).
* You must already have an **action token** from the Recurly API (`three_d_secure_action_token_id` or proactive token).
* `threeDSecure.attach()` must be called from a **user-initiated event** such as `click` or `touchend`—otherwise browsers will block the pop-up/iframe.
* By default Recurly performs gateway **device-data pre-flights**; you may disable them (`preflightDeviceDataCollector: false`) only if you are certain no challenge will be required.
* **Proactive 3DS** (authenticate before the first transaction) is currently available **only for Braintree**; enable it via `risk.threeDSecure.proactive` in `recurly.configure`.
* Provide a visible **container element** (\~250 × 400 px) for the challenge; remove or hide it only after a `token` or `error` event.
* The resulting `three_d_secure_action_result` token **expires in 20 minutes** and cannot be recovered after expiry.

***

# Key details

Strong customer authentication for your users.

Recurly.js provides a set of utilities that allow you to support 3-D Secure authentication on your checkout page seamlessly. For more information on 3-D Secure, see our [Introduction to Strong Customer Authentication](https://docs.recurly.com/docs/revised-payment-services-directive-psd2).

Recurly's support for 3-D Secure utilizes both Recurly.js and our API. For a complete guide to this integration, start with our [Strong Customer Authentication (SCA) Integration Guide](https://docs.recurly.com/v1.1/docs/3d-secure-20-integration-guide#/).

Let's take a look at an example implementation.

```javascript
const risk = recurly.Risk();
const threeDSecure = risk.ThreeDSecure({
  actionTokenId: myActionTokenId
});

threeDSecure.on('token', function (token) {
  // handle passing the action result
  // token back to your server

  // token.type => 'three_d_secure_action_result'
  // token.id

  // optionally, you may call threeDSecure.remove() to remove the element
});

threeDSecure.on('error', function (error) {
  // handle error scenarios.

  // error.code
  // error.message

  // optionally, you may call threeDSecure.remove() to remove the element
});

threeDSecure.attach(document.querySelector('#my-auth-container'));
```

### Additional Configuration

#### Preflights

Some gateways such as World Pay and Cybersouce, have API calls as a part of\
their device fingerprinting required for 3DS2. By default if you have
Cybersource or World Pay configured in Recurly to handle 3DS2 transactions
Recurly.js will make this API calls by default to ensure your implementation is
properly handling 3DS2 challenges.

Below is an example of disabling this preflight fingerprinting for said gateways\
above.

```javascript
recurly.configure({
  // ...
  risk: {
    threeDSecure: {
      preflightDeviceDataCollector: false,
    }
  }
  // ..
});
```

You may want to use the config above, if you are handling a transaction you are\
confident will not be challenged. It is important to note, that if the issuing
bank or gateway does request a challenge, you will not be able to handle the
challenge if this configuration is in use.

#### Proactive 3D-Secure

You can take your customers through Strong Customer Authentication before their initial transaction to store their card details by enabling Proactive 3D-Secure.\
This feature is currently available for the following gateways:

* Braintree

Below is an example for enabling this feature.

```javascript
recurly.configure({
  // ...
  risk: {
    threeDSecure: {
      proactive: {
        enabled: true,
        gatewayCode: "abc123",
        amount: 0.00
      },
    }
  }
  // ..
});
```

With proactive 3D-Secure enabled, you will receive a `three_d_secure_proactive_action_token` when you\
tokenize your billing information. This value can be passed in as an `actionTokenId` to the `threeDSecure`
class to enable the Strong Customer Authentication flow.

### Reference

#### <span class="heading-tag heading-tag--fn">fn</span> recurly.Risk

##### Arguments

None.

##### Returns

A new `Risk` instance

#### <span class="heading-tag heading-tag--fn">fn</span> recurly.ThreeDSecure

##### Arguments

| Param                 | Type     | Description                                                                                                                                                             |
| :-------------------- | :------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| options               | `Object` |                                                                                                                                                                         |
| options.actionTokenId | `String` | `three_d_secure_action_token_id` OR `three_d_secure_proactive_action_token_id` returned by the Recurly API when 3-D Secure authentication is required for a transaction |

##### Returns

A new `ThreeDSecure` instance.

#### <span class="heading-tag heading-tag--fn">fn</span> threeDSecure.attach

##### Arguments

| Param     | Type          | Description                                                                                                                                                                                                                                                                                            |
| :-------- | :------------ | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| container | `HTMLElement` | A DOM element to contain any UI elements necessary to fulfill the 3-D Secure authentication process. We recommend this element be a minimum size of 250px (W) x 400px (H), and contain an interstitial message to explain that 3-D Secure authentication will be required to complete the transaction. |

##### Returns

Nothing

##### <span class="heading-tag heading-tag--event">events</span> Emits

##### `token`

This event is fired when your customer has completed the 3-D Secure flow. Recurly has received the authentication details, and generated this token to be used in our API.

##### Signature

| Param      | Type     | Description                            |
| :--------- | :------- | :------------------------------------- |
| token      | `Object` |                                        |
| token.type | `String` | 'three\_d\_secure\_action\_result'     |
| token.id   | `String` | Token identifier to be sent to the API |

##### `error`

This event is emitted when any error is encountered, whether during setup of the 3-D Secure flow, or during authentication. It will be useful to display errors to your customer if a problem occurs during the 3-D Secure flow.

##### Signature

| Param | Type           | Description                                  |
| :---- | :------------- | :------------------------------------------- |
| error | `RecurlyError` | An error describing the issue that occurred. |

***