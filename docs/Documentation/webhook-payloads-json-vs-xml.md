---
title: 'Webhook payloads: JSON vs. XML'
excerpt: >-
  Compare JSON and XML webhook payload formats and learn why JSON is
  recommended.
deprecated: false
hidden: false
metadata:
  robots: index
---
Recurly webhooks can deliver notifications in either JSON or XML. You choose a format per endpoint. JSON payloads are lightweight, modern, and align with our [best practices](https://docs.recurly.com/v1.3/docs/best-practices#/), while XML provides a fuller representation of the resource.

# Prerequisites and limitations

* Each webhook endpoint must be set to either JSON **or** XML (not both).
* Existing integrations may need parsing logic for the chosen format.
* JSON payloads include only event type, object context, and identifiers—full resource data must be fetched via the API.

# Key details

Webhook payloads are available in JSON or XML. Each webhook endpoint can be configured to receive one payload type or the other. The JSON webhooks provide light weight payloads containing information about the type of event (e.g. update, create, etc.), context regarding the object (e.g. Account, Subscription) and object identifiers that can be used to obtain the most up to date information via Recurly API. Recurly highly recommends usage of the JSON webhooks as they are our most modern offering and they encourage our [best practice](https://docs.recurly.com/v1.3/docs/best-practices#/) approach.