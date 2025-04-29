---
title: Sandbox vs Production
excerpt: >-
  Short-lived sandbox webhooks are isolated from production webhooks; heavy test
  traffic can delay sandbox deliveries for up to 48 hours, and Recurly may
  auto-pause endpoints that repeatedly fail.
deprecated: false
hidden: false
metadata:
  robots: index
---
Recurly sends sandbox and production webhooks from different services, so that the high level of testing done in sandbox mode does not impact production data.

In times of high sandbox testing volume, sandbox webhook deliverability may be delayed by up to **48 hours**.

Recurly may auto-pause sandbox webhook endpoints that consistently return a high number of deliverability errors in a short window of time.