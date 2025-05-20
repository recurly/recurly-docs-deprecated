---
title: Configuration and security
excerpt: >-
  Guidance on authenticating, retrying, and protecting your Recurly webhook
  endpoints.
deprecated: false
hidden: false
metadata:
  robots: index
---
This section covers how Recurly handles webhook retries, how to authenticate incoming webhook requests, and ways to secure your endpoint.

### Prerequisites and limitations

* Configure HTTP Basic Authentication if you wish to verify incoming requests.
* Restrict access to Recurly’s IP ranges (see IP Whitelisting link below).
* Web-application firewalls (e.g., ModSecurity) may block webhook traffic unless adjusted.

# Key details

If Recurly fails to deliver a webhook, it will retry it (see [Automatic Retries](https://docs.recurly.com/v1.3/docs/automatic-retries#/), below).

Webhooks support **HTTP Basic Authentication** to verify the request came from Recurly's servers.

Please see our [IP Whitelisting documentation](https://docs.recurly.com/v1.0/docs/ip-allowlist#/) for the current list of Recurly IPs.\
You may refuse other IP addresses at your endpoint or firewall.

<Cards columns={1}>
  <Card title="ModSecurity Users" icon="fa-exclamation-triangle">
    Recurly does not endorse any specific web server or plugin, but if you run Apache with ModSecurity, you may need to disable rule #990011 so webhook requests are not blocked.
  </Card>
</Cards>