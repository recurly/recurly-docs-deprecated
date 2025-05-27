---
title: Using 3D secure with stored Billing Information
deprecated: false
hidden: true
metadata:
  robots: index
---
# Overview

This guide shows you how to use the [Purchase endpoint](https://developers.recurly.com/api/latest/#tag/purchase) to create new accounts, add subscriptions or one-time charges, and securely capture payment details. We’ll also illustrate how to integrate [Recurly.js](https://developers.recurly.com/reference/recurly-js) for secure tokenization.

### Prerequisites & limitations

* Familiarity with Recurly’s API and basic REST concepts
* [Completed the Quickstart Guide](https://docs.recurly.com/v1.1/docs/quick-start-guide#/) and [3DS integration guide](https://docs.recurly.com/v1.1/docs/3d-secure-20-integration-guide#/versions)
* Conditional usage of [Recurly.js](https://developers.recurly.com/reference/recurly-js) depending on your supported gateway:
  * Cybersource and WorldPay gateways require use of Recurly.js to complete this integration guide

***

# Definition

**Stored Billing Information** refers to card data stored in Recurly's systems and referenced via API by sending in an account code, or specify a billing ID when using Recurly Wallet.

**PSD2** refers to the EU mandate for SCA, or Strong Customer Authentication, and is applicable to EU merchants mostly. Read more about [PSD2 in our compliance documentation](https://docs.recurly.com/docs/revised-payment-services-directive-psd2#/) for 3DS, SCA, and PSD2.

**Reactivating** a cancelled Subscription is referring to the API path in Recurly APIs where a customer's cancelled subscription is reactivated and resumes renewal billing again. You can view documentation on [reactivating a cancelled subscription via API](https://recurly.com/developers/api/v2021-02-25/index.html#operation/reactivate_subscription) in our developer hub.

**Resuming** a paused Subscription is referring to the API path in Recurly APIs where a customer's paused subscription is resumed and starts billing again. You can view documentation on [resuming a paused subscription via API](https://recurly.com/developers/api/v2021-02-25/index.html#operation/resume_subscription) in our developer hub.

***

# 3DS prior to Reactivating or Resuming a Subscription

## Step 1: Submit a Verification Request via API

When a customer who has a paused subscription requests that subscription is resumed, prior to resuming that subscription, use the API to request billing info verification using an account code, or a billing info ID if using Recurly Wallet, that is attached to that subscription.

You can do this in one of two ways depending on your preference:

* With CVV: [Verify an account's credit card billing cvv](https://recurly.com/developers/api/v2021-02-25/index.html#operation/verify_billing_info_cvv)
* Without CVV: [Verify an account's credit card billing information](https://recurly.com/developers/api/v2021-02-25/index.html#operation/verify_billing_info)

Once you've done this, if 3DS challenge is required, you will receive a `three_d_secure_action_token_id` as documented in the [3DS integration guide](https://docs.recurly.com/v1.1/docs/3d-secure-20-integration-guide#/versions). From here, follow the flows outlined in the [3DS integration guide](https://docs.recurly.com/v1.1/docs/3d-secure-20-integration-guide#/versions) to complete 3DS for re-verification.

Use [Recurly.js](https://developers.recurly.com/reference/recurly-js/#getting-started) to submit the 3DS action token and resubmit the verification using the action result token. Once you have a successful reverification transaction response from the gateway, you may move on to Step 2.

**Note:** If you are using Cybersource or WorldPay, you will want to *start* this process with Recurly.js and pass in the billing info ID or account code to Recurly.js and pass in a `token_id` to one of the above two endpoints. This is because Cybersource and WorldPay require a data collector to capture consumer information for 3DS to function properly on those platforms.

### Handling Re-verification and 3DS Authentication Failures

Consumers can fail SCA for a multitude of reasons including cancelling out of the challenge window, browsers blocking pop-up modals, account takeover / fraudulent attempts, and more. You may offer consumers multiple chances to resume their subscription as per your own business needs. It is recommended to request new billing information after a few attempts to reverify existing billing information.

## Step 2: Resume or Reactivate the Subscription

**Resuming a Paused Subscription**: If successful, you can [resume the paused subscription](https://recurly.com/developers/api/v2021-02-25/index.html#operation/resume_subscription) by implementing the Resume Subscription endpoint and reference the subscription ID in your path.

Read more about subscription lifecycles in our dedicated [Subscription lifecycle documentation](https://docs.recurly.com/docs/subscription-lifecycle#/).

**Reactivating a Cancelled Subscription** If successful, you can [reactivate the cancelled subscription](https://recurly.com/developers/api/v2021-02-25/index.html#operation/reactivate_subscription) by implementing the Reactivate Subscription endpoint and reference the subscription ID in your path.

Read more about subscription lifecycles in our dedicated [Subscription lifecycle documentation](https://docs.recurly.com/docs/subscription-lifecycle#/).

## Step 3: Verify and finish

After a successful verification and resume/reactivation, you can confirm the details via the Recurly Admin UI or by calling Recurly’s API to list your new account, subscription, or invoice.

***

## Next steps

Now that you can create new [accounts](https://app.recurly.com/go/accounts), [subscriptions](https://app.recurly.com/go/subscriptions), and one-time payments, explore the [Subscription Management](https://docs.recurly.com/v1.1/docs/managing-subscription-methods-guides#/) guide to learn how to modify subscriptions after the initial purchase.