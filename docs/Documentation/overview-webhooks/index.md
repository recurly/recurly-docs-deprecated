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
# Webhooks

Webhooks let you notify your internal systems and partner applications whenever something important happens in Recurly. Treat them as **alerts**—not as the sole source of truth—then follow the [Best Practices](https://docs.recurly.com/v1.3/docs/best-practices#/) section to act on those events safely.

Recurly can post notifications to any publicly reachable server. When a qualifying event occurs (for example, a new account is created), Recurly sends a webhook to each endpoint you configure—up to **10 endpoints** per site. Every endpoint receives the notifications for the [lifecycle events](https://docs.recurly.com/v1.3/docs/lifecycle-events#/) it is subscribed to.

A notification counts as **delivered** only when Recurly gets a timely, successful response:

* The endpoint must listen on port **80 (HTTP)** or **443 (HTTPS)**—other ports are not supported.
* The endpoint must reply within **5 seconds**.
* The response must be an HTTP **2XX** status (200, 201, 204, etc.). Recurly does not follow redirects, and non-2XX responses are treated as failures.

Need a quick test endpoint? Try <a href="https://requestbin.com/">RequestBin</a> or <a href="https://mockbin.org/">Mockbin</a>.