---
title: Best practices
excerpt: >-
  Practical guidance for safely consuming Recurly webhooks—covering idempotency,
  ordering, and why every webhook should be verified with a follow-up API call.
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

\<Cards columns=\{1}>
&#x20; \<Card title="Example" href="https\://readme.com" icon="fa-home">
An account is closed and a webhook is sent. Delivery fails, so Recurly schedules a retry.\\
Before the retry succeeds, the customer reopens the account, generating a second webhook.
When your endpoint comes back online, it might receive the \*closed\* notification \*\*after\*\* the \*reopened\* one. \*\*Always\*\* verify the current account status via the API before acting on the webhook payload.  \</Card>
\</Cards>