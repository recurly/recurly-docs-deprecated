---
title: Best practices
excerpt: >-
  Practical guidance on how to consume Recurly webhooks safely—when to treat
  them as alerts, how to guard against duplicates, and why you must always
  verify state with the API.
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

<Cards columns={1}>
  <Card title="Example" icon="fa-info-circle">
    For example, an account can close and we will send a notification for this. If delivery fails, the notification will be sent again later. In the meantime, the account could reopen (triggering another push notification). If your endpoint begins working again, it may receive the closed-account notification **after** the account was reopened. Make sure that, if your application takes action on closed accounts, it verifies the account is still closed by issuing an API request.
  </Card>
</Cards>