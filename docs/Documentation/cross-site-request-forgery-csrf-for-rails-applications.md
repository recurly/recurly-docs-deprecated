---
title: Cross site request forgery (CSRF) for Rails applications
excerpt: How to disable Rails CSRF protection for your Recurly webhook endpoint.
deprecated: false
hidden: false
metadata:
  robots: index
---
Rails’ built-in CSRF protection (`protect_from_forgery`) will block incoming POSTs from external services like Recurly webhooks. To receive webhook notifications, you must exempt only the specific controller action that handles them.

# Prerequisites and limitations

* Your Rails app must have `protect_from_forgery` enabled (default for new apps).
* Only the webhook listener action should be excluded—keep CSRF protection enabled for all other actions.

# Key details

In your controller, disable forgery protection for the action that listens for Recurly notifications (assuming it’s named `recurly_notification`):

```ruby
protect_from_forgery :except => :recurly_notification
```