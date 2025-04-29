---
title: Best practices
excerpt: >-
  Webhooks are not actionable on their own and should not be used for critical
  functions like provisioning accounts. The API response from an original action
  (i.e. signup, one time purchase) can be used to provision the account and
  store the state/details behind the action locally. The state/details of a user
  should be maintained in your internal database, and assumed unchanged unless a
  change of state is indicated with a webhook. Use the receipt of a webhook to
  trigger an API query to validate the push notification details against the
  current API data.  Recurly webhooks may be retried or sent multiple times if
  the delivery status is considered failed. Please make sure your endpoint can
  receive the same notification multiple times and in the wrong order.  For
  example, an account can close and we will send a notification for this. If
  delivery fails, the notification will be sent again later. In the meantime,
  the account could reopen (triggering another push notification). If your
  endpoint begins working again, it may receive the closed account notification
  after the account was reopened). Make sure that if your application takes
  action on closed accounts, that it verifies the account is still closed by
  issuing an API request.
deprecated: false
hidden: false
metadata:
  robots: index
---
Webhooks are **notifications**, not commands. Use them to discover that something **might** have changed in Recurly, then confirm the authoritative state with the Recurly API before you update your own systems.

### Prerequisites and limitations

* Your endpoint already meets the connectivity rules outlined in **Webhooks** (publicly reachable on port 80/443 and capable of replying with a **2XX** within 5 seconds).
* Recurly can retry or resend a webhook, so duplicate deliveries are expected.
* Retries may arrive **out of order**—your logic must handle older events that follow newer ones.

# Key details

Webhooks are not actionable on their own and should **not** be used for critical functions such as provisioning accounts. The API response from the originating action (e.g., signup, one-time purchase) should provision the account and store the resulting state in your own database. Treat that local state as correct **unless** a webhook indicates a change.

When a webhook arrives, use it as a trigger to:

1. **Call the Recurly API** to confirm the current status of the resource.
2. **Compare** the payload to your local record.
3. **Update** your database only if the API shows a real change.

Recurly webhooks may be retried or sent multiple times if delivery is considered failed. Your endpoint **must**:

* Accept the same notification more than once.
* Tolerate events that arrive in the wrong order.

\<Cards columns=\{1}>\
\<Card title="Example" href="[https://readme.com](https://readme.com)" icon="fa-home">
An account is closed and a webhook is sent. Delivery fails, so Recurly schedules a retry.\
Before the retry succeeds, the customer reopens the account, generating a second webhook.
When your endpoint comes back online, it might receive the *closed* notification **after** the *reopened* one. **Always** verify the current account status via the API before acting on the webhook payload.  \</Card>
\</Cards>

\<Cards columns=\{1}>
&#x20; \<Card title="Example" icon="fa-home">
An account is closed and a webhook is sent. Delivery fails, so Recurly schedules a retry.\\\\\\
Before the retry succeeds, the customer reopens the account, generating a second webhook.
When your endpoint comes back online, it might receive the \*closed\* notification \*\*after\*\* the \*reopened\* one. \*\*Always\*\* verify the current account status via the API before acting on the webhook payload.\</Card>
\</Cards>