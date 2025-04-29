---
title: Webhook details
excerpt: How Recurly delivers, stores, and displays individual webhook notifications.
deprecated: false
hidden: false
metadata:
  robots: index
---
Each webhook is sent as a separate notification, retained for 15 days, and viewable in the Recurly console with full delivery status and error details.

### Prerequisites and limitations

* Notifications are stored for **15 days** only.
* Deleting an endpoint will remove its last 15 days of notifications from the console.

# Key details

Notifications are always delivered separately. For example, if a customer signs up for a subscription and that action triggers a payment, you will receive two distinct notifications—one for the subscription event and another for the payment event.

All webhook notifications are retained in our system for **15 days** and can be accessed via the application console, which also displays failure reasons. While each notification’s timestamp is recorded in UTC, it is presented in your site’s configured time zone when you view notification status.

> **Note:** If you delete an endpoint, any notifications sent to that endpoint within the past 15 days will no longer appear in the console.