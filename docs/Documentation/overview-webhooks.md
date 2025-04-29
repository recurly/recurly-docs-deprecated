---
title: 'Overview: Webhooks'
excerpt: >-
  Receive real-time notifications from Recurly—learn how to configure, secure,
  and consume webhook events to keep your internal systems and partners in sync.
deprecated: false
hidden: false
metadata:
  robots: index
---
**Webhooks** let **Recurly** push event data to a URL you control—no polling required. Whenever something meaningful happens in your site (for example, a subscription renews or a payment fails) Recurly sends a signed JSON payload to each subscribed endpoint. Your application can then update internal records, kick off workflows, or alert downstream partners.

> **Tip** Webhooks are designed for *notification* rather than *source-of-truth* processing. Always confirm details with the Recurly API before taking irreversible action. See our**Best Practices** page.

***

### Prerequisites & limitations

* Your listener **must** be publicly reachable on port `80` or `443`.
* It must return an HTTP **2xx** status within **5 seconds**—otherwise Recurly queues a retry.
* Maximum **10 endpoints** per Recurly site.
* Recurly does **not** follow redirects; 3xx, 4xx, or 5xx responses are treated as failures.
* Use HTTPS whenever possible; self-signed certificates are not supported.

***

### Key details

* **Event coverage**: Receive notifications for account, subscription, invoice, credit, transaction, dunning and gift-card lifecycle events. [Learn more](#lifecycle-events)
* **Delivery guarantee**: Recurly retries failed webhooks exponentially for up to seven days; each attempt includes an HMAC-SHA256 signature for verification. [Learn more](#security-and-retries)
* **Endpoint management**: Add, edit, pause or delete endpoints in **Developers → Webhooks** (UI) or via the API. [Learn more](#configuring-endpoints)
* **Testing tools**: Use services such as RequestBin or Mockbin to inspect payloads before going live. [Learn more](https://requestbin.com/)

***