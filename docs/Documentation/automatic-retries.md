---
title: Automatic retries
excerpt: >-
  How Recurly automatically retries failed webhook deliveries, including retry
  limits and exponential backoff timing.
deprecated: false
hidden: false
metadata:
  robots: index
---
When a webhook delivery to your endpoint fails, Recurly will automatically retry sending the notification using an exponential backoff strategy. This helps ensure reliable delivery without overloading your server.

# Key details

* If Recurly receives a non-2XX response from your webhook endpoint, it will schedule the notification for retry.
* Each notification is retried up to **10** times; after the tenth failure, no further attempts are made.
* Retries occur in the same order notifications were created.
* The interval before each retry grows exponentially, approximately following:

```scss
10 + x * 2^(x + 5) seconds
```

Where **x** is the current retry attempt (0-indexed). Early retries happen quickly, and later retries space out substantially.