---
title: Recurly
excerpt: ''
deprecated: false
hidden: true
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
## Overview

<HTMLBlock>{`
<iframe width="712" height="400" src="https://www.loom.com/embed/46bc074c2ae84fcd8fd55d7b342859d2?sid=d12f25e0-c01d-4ce7-b63c-a6c26f28f83d" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
`}</HTMLBlock>

## Activation

Generate an API key from the Recurly console and paste it in to the Settings &gt; Integrations &gt; Recurly modal. If your application references its own User IDs for Recurly accounts/subscriptions, make sure to toggle the "Use Account Code" to On (this should be the case most of the time). Finally, make sure to toggle Active to On.

- API Key ([Instructions](https://docs.recurly.com/docs/api-keys))

## Data Integration

Automated exports from Recurly may be configured to automatically sync on a nightly basis. Follow these [instructions](https://docs.recurly.com/docs/redfast#step-4-import-user-traits-using-both-recurly-and-redfast-sites) to enable the automated sync. Once active, the following traits are synced nightly.

| Trait Name                | Description                                                                         |
| :------------------------ | :---------------------------------------------------------------------------------- |
| state                     | Current state of subscription (pending, active, canceled, expired)                  |
| plan_code                 | Plan that the customer is subscribed to                                             |
| currency                  | Identifies the currency being charged with the subscription.                        |
| current_period_started_at | Date and time that the current billing period starts at.                            |
| current_period_ends_at    | Date and time that the current billing period ends at                               |
| trial_started_at          | Date and time that a trial period began on the subscription.                        |
| trial_ends_at             | Date and time that a trial period ends on the subscription.                         |
| activated_at              | Date and time the subscription became active on an account.                         |
| canceled_at               | Date and time the subscription was canceled.                                        |
| expires_at                | Date and time the subscription was/ will churn.                                     |
| status                    | Current status of the invoice (pending, processing, past_due, paid, failed, voided) |
| maintenance_url           | Link to the customer's hosted account maintenance URL (if enabled)                  |

## Supported Actions

| Action                    | Description                                                 | API Integration         | Additional Instructions                     |
| ------------------------- | ----------------------------------------------------------- | ----------------------- | ------------------------------------------- |
| Apply Coupon Code         | Applies a coupon code to the user's account or subscription | Coupon Redemption       | Select the coupon code                      |
| Pause Subscription        | Pauses a user's subscription                                | Pause Subscription      | Select how many billing cycles to pause for |
| Resume Subscription       | Resumes a paused subscription                               | Resume Subscription     |                                             |
| Switch Subscription       | Switches the user to a new plan                             | Subscription Change     | Select the plan                             |
| Create Subscription       | Creates a subscription for an existing account              | Create Subscription     | Select the plan and enter the currency      |
| Reactivate Subscription   | Reactivate a cancelled subscription                         | Reactivate Subscription |                                             |
| Update Subscription Price | Update price of an active subscription                      | Subscription Change     |                                             |
| Convert Trial             | Convert trial to paid subscription                          | Convert Trial           |                                             |
| Record Usage              | Logs a usage record for a subscription add-on               | Log Usage Record        | Select the add-on and amount                |
| Cancel Subscription       | Stops auto-renewal for an active subscription               | Cancel Subscription     | Select refund option                        |