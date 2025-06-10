---
title: Getting started
excerpt: >-
  A comprehensive guide to getting started with Recurly.js—covering script
  integration, configuration, and building a secure, PCI-compliant payment form
  using Elements.
deprecated: false
hidden: false
metadata:
  robots: index
---
To begin, include the Recurly.js script on your page:

```html
<script src="https://js.recurly.com/v4/recurly.js"></script>
<link href="https://js.recurly.com/v4/recurly.css" rel="stylesheet" type="text/css">
```

> **Note:** Including `recurly.css` is optional but recommended as it provides helpful default styles for Recurly Elements during setup.

### Prerequisites & limitations

* You must have a valid Recurly account with API access.
* Basic familiarity with HTML, JavaScript, and RESTful APIs.
* Using Recurly.js is optional but recommended for enhanced security and reduced PCI scope.

***

# How it works

Recurly.js streamlines your checkout by securely capturing customer payment information. When a customer submits your payment form, Recurly.js encrypts their payment data and stores it on our servers, returning an authorization key (or *token*). With this token, you can complete the subscription process using our API—without ever directly handling sensitive payment data. This reduces your PCI compliance requirements significantly.

***

# Self-hosting Recurly.js

We highly recommend using the Recurly-hosted version of Recurly.js. This version is updated to maintain compatibility with Recurly’s system updates. Locally hosted copies may encounter integration issues or service interruptions.

***

# Configure

Call `recurly.configure` anywhere on your page, passing your public key to identify your site to Recurly servers. You can find your public key in the [API Access section](https://app.recurly.com/go/developer/api_access) of your admin app.

```js
recurly.configure('sc-ABCDEFGHI123456789');
```

**Use Your Site's Public Key:** Replace `sc-ABCDEFGHI123456789` with your own public key.

*Note:`recurly.configure` accepts additional options. For further details, refer to the [source](https://github.com/recurly/recurly-js/blob/master/lib/recurly.js#L48).*

***

# Build a payment form with elements

Design an HTML form to collect customer payment details. Use the `data-recurly` attribute to identify non-card billing fields. Recurly.js builds the card fields using Elements.

```html
<form id="my-form">
  <input type="text" data-recurly="first_name" placeholder="First Name">
  <input type="text" data-recurly="last_name" placeholder="Last Name">

  <div id="recurly-elements">
    <!-- Recurly Elements will be attached here -->
  </div>

  <!-- Recurly.js will update this field automatically -->
  <input type="hidden" name="recurly-token" data-recurly="token">

  <button type="submit">Submit</button>
</form>
```

***

# The card element

<Image align="center" width="80% " src="https://files.readme.io/5a6031def261981056429456e6e339475283e49e59948602bf500c497b358182-image.png" />

The Card Element is the simplest way to accept customer card data. Recurly.js injects a single, responsive field for the card number, expiry, and CVV. This field includes helpful UX enhancements for smooth entry on any device.

```js
const elements = recurly.Elements();
const cardElement = elements.CardElement();
cardElement.attach('#recurly-elements');
```

***

# Other elements

Recurly.js also offers individual Elements for separate card fields if a combined field doesn't fit your design needs. See the [Elements documentation](#elements) for further details.

***

# Billing fields

Below is a table of potential billing fields supported by Recurly.js. Some fields may be required depending on your [billing address requirements](https://docs.recurly.com/site-settings#address_requirements).

| Field Name            | Example          | Description                                                              |
| --------------------- | ---------------- | ------------------------------------------------------------------------ |
| first\_name           | `Ben`            | Cardholder first name (**Required**)                                     |
| last\_name            | `du Monde`       | Cardholder last name (**Required**)                                      |
| address1              | `1313 Main St.`  | First line of a street address                                           |
| address2              | `Unit 1`         | Second line of a street address                                          |
| city                  | `Hope`           | Town or locality                                                         |
| state                 | `WA`             | Province or region                                                       |
| postal\_code          | `98552`          | Postal code                                                              |
| country               | `US`             | ISO 3166-1 alpha-2 country code                                          |
| phone                 | `555-867-5309`   | Phone number                                                             |
| vat\_number           | `SE0000`         | Customer VAT number (used for VAT exclusion)                             |
| tax\_identifier       | `972.791.615-53` | A valid CPF or CUIT (required for consumer cards in Brazil or Argentina) |
| tax\_identifier\_type | `cpf` or `cuit`  | Accepted values: `cpf` or `cuit`                                         |