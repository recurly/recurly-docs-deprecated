---
title: Webhooks states
excerpt: >-
  Definitions and explanations of the possible delivery states for Recurly
  webhook notifications.
deprecated: false
hidden: false
metadata:
  robots: index
---
Every webhook notification in Recurly is assigned a state reflecting its delivery progress. You can filter and view notifications by these states in the Recurly console to monitor and troubleshoot your webhook integrations.

# Key details

Notifications may appear in one of these states:

* **Pending**: Queued and awaiting delivery.
* **Delivered**: Successfully sent and acknowledged.
* **Retrying**: Delivery failed and will be attempted again later.
* **Failed**: All retry attempts exhausted; will not be retried automatically.
* **Paused**: Stored without any delivery attempts.