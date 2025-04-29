---
title: Lifecycle events
excerpt: >-
  Manage and customize which Recurly lifecycle event notifications each webhook
  endpoint receives.
deprecated: false
hidden: false
metadata:
  robots: index
---
Recurly lets you opt in to only the specific lifecycle event notifications you need—on a per-endpoint basis—so you receive just the alerts relevant to your integration.

### Prerequisites and limitations

* You must configure each webhook endpoint in the Recurly admin to subscribe to the desired events.
* Each site supports up to 10 webhook endpoints.
* Event subscription changes take effect immediately for all subsequent notifications.

# Key details

It is possible to opt in to only the specific notifications you would like to receive, on an endpoint-by-endpoint basis. For example, if you have an endpoint that only needs to receive notifications about new accounts but not account updates, you can configure that endpoint accordingly.

A full list of all notifications can be found in our **[Account Notifications](https://docs.recurly.com/v1.3/docs/account-notifications#/)** dedicated page, along with both the JSON and XML versions of each payload.