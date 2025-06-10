---
title: Pricing
excerpt: >-
  Programmatically calculate real-time checkout totals—plans, add-ons, coupons,
  taxes, adjustments, gift cards, multi-subscription carts, and more—using
  `recurly.Pricing.Checkout` in Recurly.js.  
deprecated: false
hidden: false
metadata:
  robots: index
---
`recurly.Pricing.Checkout` is a client-side pricing engine that mirrors Recurly’s server logic. Attach it to a section of your checkout (or drive it purely through code) and it will keep subtotals, discounts, taxes, and grand totals in sync as customers pick plans, quantities, coupons, gift cards, or custom adjustments. The class emits events on every change, so you can update UI elements or trigger additional logic without extra API calls.

## Prerequisites and limitations

* **Site configuration:** The plans, add-ons, coupons, gift cards, and taxes referenced in your checkout must exist—and be active—on the Recurly site whose public key you’re using.
* **Currency:** All items in a single `CheckoutPricing` instance must share the same currency code. Change the currency via the `data-recurly="currency"` input or the API before adding items.
* **One-way DOM binding:** If you call the JavaScript API methods on a pricing instance that’s already `attach`-ed to DOM elements, those input/output elements will **not** auto-update; use API-only mode or re-render values yourself.

# Key details

Recurly automates complicated subscriptions, with many factors influencing the total price at\
checkout. With this in mind, Recurly.js provides a robust `recurly.Pricing.Checkout` class designed
to make determining the actual checkout costs as simple and flexible as possible.

A Recurly.js checkout pricing instance can be attached to the form we created above, or to any other\
section of your page meant to display pricing.

### Reference

#### <span class="heading-tag heading-tag--fn">fn</span> recurly.Pricing.Checkout

```javascript
const checkoutPricing = recurly.Pricing.Checkout();
```

Creates a `CheckoutPricing` instance.

##### No Arguments

##### Returns

A new `CheckoutPricing` instance.

#### <span class="heading-tag heading-tag--fn">fn</span> CheckoutPricing.attach

Given the following example HTML structure

```html
<section id="my-checkout">
  <select data-recurly="plan">
    <option value="basic">Basic</option>
    <option value="notbasic">Not Basic</option>
  </select>
  <input type="text" data-recurly="coupon">
</section>
```

Use `checkoutPricing.attach` to bind the `<section>` to the checkout pricing calculator.

```javascript
const checkoutPricing = recurly.Pricing.Checkout();

checkoutPricing.attach('#my-checkout');
```

This is the simplest way to use a `CheckoutPricing` instance. Simply pass a container element, and the\
`CheckoutPricing` instance will use all elements with a valid `data-recurly` attribute to determine
price. When a value changes, the `CheckoutPricing` instance will automatically update its calculations.

This allows your customers to manipulate your checkout form at will, and have your checkout page's\
prices update automatically.

##### Arguments

| Param     | Type                      | Description                                                                                                                     |
| :-------- | :------------------------ | :------------------------------------------------------------------------------------------------------------------------------ |
| container | `HTMLElement` or `String` | Parent element containing all `data-recurly` elements. If a `String` is passed, it will be parsed with `document.querySelector` |

##### Returns

Nothing.

##### HTMLElements

`HTMLElement`s bound to a `CheckoutPricing` instance may be for either input or output.

##### Input HTMLElements

Input elements should be user-manipulable elements like `input` or `select`. If you want to input a\
value but not let a customer manipulate it, use an `<input type="hidden">`.

###### Subscriptions

`CheckoutPricing` instances can calculate a checkout containing **one or more** subscriptions. The\
table below lists all available input `HTMLElement` types.

| `data-recurly` value | Example Value | Description                                                                                                         |
| :------------------- | :------------ | :------------------------------------------------------------------------------------------------------------------ |
| plan                 | `basic`       | Plan code.                                                                                                          |
| plan\_quantity       | `1`           | Plan quantity. Defaults to 1 if not specified                                                                       |
| addon                | `1`           | Addon quantity. To identify the addon being modified, use the `data-recurly-addon` attribute to set the addon code. |
| tax\_code            | `digital`     | Product tax code.                                                                                                   |

If you would like to calculate multiple subscriptions on a single checkout, group the values for\
each subscription using the `data-recurly-subscription` attribute. Simply set them to a unique
id to group them. Here's an example:

```html
<section id="my-checkout">
  Subscription 1
  <select data-recurly="plan" data-recurly-subscription="0">
    <option value="basic">Basic</option>
    <option value="notbasic">Not Basic</option>
  </select>

  Subscription 1 Add-ons
  <span data-recurly="addon_price" data-recurly-subscription="0" data-recurly-addon="my_addon_code_1"></span>
  <input type="text" data-recurly="addon" data-recurly-subscription="0" data-recurly-addon="my_addon_code_1" value="0">

  Subscription 2
  <select data-recurly="plan" data-recurly-subscription="1">
    <option value="basic">Basic</option>
    <option value="notbasic">Not Basic</option>
  </select>
  <input type="hidden" data-recurly="tax_code" data-recurly-subscription="1" value="my_tax_code_0">
</section>
```

###### Adjustments

As with subscriptions, `CheckoutPricing` instances can calculate a checkout containing **one or more**\
adjustments.

**Using an item code**

You may specify an item code from your Recurly catalog. This will automatically retrieve the pricing\
information for your item.

| `data-recurly` value | Example Value | Description                                                                                                                                                          |
| :------------------- | :------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| adjustment           | `0`, `1`      | **Adjustment quantity.** \<br>\<br> Set a unique identifier using `data-recurly-adjustment`. \<br>\<br> Set the item code using `data-recurly-adjustment-item-code`. |

*Example*:

```html
<section id="my-checkout">
  My product
  x
  <input type="text"
         data-recurly="adjustment"
         data-recurly-adjustment="my-adjustment-0"
         data-recurly-adjustment-item-code="my-item-code"
         value="0">
</section>
```

**Specifying adjustment properties inline**

It's also possible to specify all adjustment values inline.

| `data-recurly` value | Example Value | Description                                                                                                                                                                                                                                                                                                                                                          |
| :------------------- | :------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| adjustment           | `0`, `1`      | **Adjustment quantity.** \<br>\<br> Set a unique identifier using `data-recurly-adjustment`. \<br>\<br> Set the amount using `data-recurly-adjustment-amount`, \<br>\<br> currency with `data-recurly-adjustment-currency`, \<br>\<br> tax code with `data-recurly-adjustment-tax-code`, \<br>\<br> and tax exempt status with `data-recurly-adjustment-tax-exempt`. |

*Example*:

```html
<section id="my-checkout">
  $10 adjustment
  x
  <input type="text"
         data-recurly="adjustment"
         data-recurly-adjustment="my-adjustment-0"
         data-recurly-adjustment-amount="10"
         data-recurly-adjustment-currency="USD"
         data-recurly-adjustment-tax-code="my_tax_code_1"
         data-recurly-adjustment-tax-exempt="false"
         value="0">

  $2.99 adjustment
  <input type="checkbox"
         data-recurly="adjustment"
         data-recurly-adjustment="my-adjustment-1"
         data-recurly-adjustment-amount="2.99"
         data-recurly-adjustment-currency="USD"
         data-recurly-adjustment-tax-code="my_tax_code_2"
         data-recurly-adjustment-tax-exempt="true"
         value="1">
</section>
```

###### Other

| `data-recurly` value | Example Value | Description  |
| :------------------- | :------------ | :----------- |
| coupon               | `15_off`      | Coupon code. |
| currency             | `USD`         |              |

##### Output HTMLElements

Output elements should be plain text elements like `output`, `span`, or `div`.

| `data-recurly` value | Example Value | Description                                                              |
| :------------------- | :------------ | :----------------------------------------------------------------------- |
| total\_now           | `100.00`      | Total subscription cost due now.                                         |
| subtotal\_now        | `90.00`       | Subtotal of the following pricing components.                            |
| subscriptions\_now   | `80.00`       | Total cost of all subscriptions. This is part of the subtotal.           |
| adjustments\_now     | `10.00`       | Total cost of all adjustments. This is part of the subtotal.             |
| discount\_now        | `5.00`        | Amount discounted with coupon use.                                       |
| taxes\_now           | `15.00`       | Total subscription taxation.                                             |
| gift\_card\_now      | `75.00`       | The gift card amount that will be applied to the purchase today.         |
| gift\_card\_next     | `25.00`       | The remainder gift card amount that will be applied to the next renewal. |
| currency\_code       | `USD`, `EUR`  | ISO-4217 currency code.                                                  |
| currency\_symbol     | `$`, `€`      | Symbolic representation of `currency_code`.                              |

> **Note**: `data-recurly` values ending in `_now` like `subtotal_now` have counterparts ending in `_next`.\
> As you might expect, these correspond to the next billing cycle cost. These values are especially
> useful for subscriptions with trial periods.

##### <span class="heading-tag heading-tag--event">events</span> Emit

`CheckoutPricing` instances emit events when various values are set or the price changes. See\
[Events](#events) for usage.

| Event name            | Payload                                        | Occurs When                                                                                                    |
| :-------------------- | :--------------------------------------------- | :------------------------------------------------------------------------------------------------------------- |
| *change*              | [`PricingState`](#example-pricingstate-object) | The price changes for any reason.                                                                              |
| *set.subscription*    | Subscription object                            | A subscription is added to the checkout.                                                                       |
| *set.plan*            | Plan object                                    | A plan is set on any subscription.                                                                             |
| *set.addon*           | Addon object                                   | An addon is set on any subscription.                                                                           |
| *set.adjustment*      | Adjustment object                              | An adjustment is added to the checkout.                                                                        |
| *set.address*         | Address object                                 | The user's address is changed on the checkout. This is limited to country, postalCode, and vatNumber.          |
| *set.shippingAddress* | Address object                                 | The user's shipping address is changed on the checkout. This is limited to country, postalCode, and vatNumber. |
| *set.tax*             | Tax properties object                          | Tax properties are changed on the checkout.                                                                    |
| *set.currency*        | Currency code                                  | The currency is set on the checkout.                                                                           |
| *set.coupon*          | Coupon object                                  | A valid coupon is set on the checkout.                                                                         |
| *unset.coupon*        | Coupon object                                  | An existing valid coupon is removed from the checkout.                                                         |
| *error.coupon*        | RecurlyError                                   | A requested coupon code is invalid.                                                                            |
| *set.gift\_card*      | Gift card object                               | A gift card is set on the checkout.                                                                            |
| *unset.gift\_card*    | Nothing                                        | A gift card is removed from the checkout.                                                                      |

### CheckoutPricing API

A `CheckoutPricing` instance can be manipulated with a set of direct method calls. This is useful\
if you would like to set up a complex pricing schema for your customers, or would just like to
use a more programmatic method of determining checkout price. Events fire just as they normally
would when using `CheckoutPricing.attach`.

Note that Recurly.js's DOM binding is one-way. Thus if you use the `CheckoutPricing` API on a pricing\
instance already attached to a container, the elements within will not update with your
`CheckoutPricing` API calls. If you would like two-way DOM binding, we suggest using the
`CheckoutPricing` API without attaching it to a container. If using [React](https://reactjs.org) ,
consider using our **[react-recurly library](https://github.com/recurly/react-recurly)**, which contains
[a custom hook for interacting with the CheckoutPricing API](https://recurly.github.io/react-recurly/?path=/docs/hooks-usecheckoutpricing--page).

The example below demonstrates all the ways that a `CheckoutPricing` instance can be manipulated\
directly.

```javascript
const checkoutPricing = recurly.Pricing.Checkout();

// Add a $10 adjustment
checkoutPricing
  .adjustment({
    id: '0',
    itemCode: 'my-item-code',
    quantity: 1,
  })
  .adjustment({
    id: '1',
    amount: 10.0,
    quantity: 1,
    taxExempt: true,
    taxCode: 'my-tax-code'
  })
  .coupon('my-coupon-code')
  .giftCard('my-gift-card-code')
  .address({
    country: 'US',
    postal_code: '90210'
  })
  .tax({
    tax_code: 'digital',
    vat_number: '',
    amounts: {
      now: '0.99',
      next: '1.99'
    }
  })
  .subscription(recurly.Pricing.Subscription()
    .plan('basic', { quantity: 1 })
    .addon('addon1', { quantity: 2 })
    .catch(function (err) {
      // err.code
    })
    .done()
  )
  .catch(function (err) {
    // err.code
  })
  .done(function (price) {
    // price object as emitted by 'change' event
  });
```

### PricingPromise

Each `CheckoutPricing` method will return a `PricingPromise`. This allows you to chain many\
asynchronous calls together without having to manage a complex series of callbacks.

You don't need to worry much about the internals of a `PricingPromise`, as it is designed to stay\
out of your way and facilitate asynchronous calls for you.

The `catch` method, as shown in the example, is used to handle error scenarios, such as when an\
addon cannot be applied to the selected plan.

> **Note**: At the end of a chain of pricing method calls, be sure to call `.done()` as this tells the Pricing\
> instance to begin calculating, and gives you the subscription price.

### Example PricingState Object

Emitted by the **change** event

```js
{
  currency: {
    code: 'USD',
    symbol: '$'
  },
  now: {
    adjustments: '20.00',
    discount: '0.00',
    giftCard: '0.00',
    items: [
      {
        addons: '0.00',
        amount: '10.00',
        id: '0',
        plan: '10.00',
        setupFee: '0.00',
        type: 'subscription'
      },
      {
        amount: '20.00',
        id: 'adj-0',
        quantity: '2.00',
        type: 'adjustment',
        unitAmount: '10.00'
      }
    ],
    subscriptions: '10.00',
    subtotal: '30.00',
    taxes: '0.00',
    total: '30.00'
  },
  next: {
    adjustments: '0.00',
    discount: '0.00',
    giftCard: '0.00',
    items: [
      {
        addons: '0.00',
        amount: '10.00',
        id: '0',
        plan: '10.00',
        setupFee: '0.00',
        type: 'subscription'
      }
    ],
    subscriptions: '10.00',
    subtotal: '10.00',
    taxes: '0.00',
    total: '10.00'
  },
  taxes: []
}
```

<br />

<br />

***