---
title: Signature verification
excerpt: >-
  Learn how to verify Recurly webhook signatures to ensure notifications are
  authentic and untampered.
deprecated: false
hidden: false
metadata:
  robots: index
---
For all JSON‐formatted webhooks, Recurly includes a `recurly-signature` header. Verifying this signature proves the notification came from Recurly and has not been altered. You can verify signatures manually or by using one of our client libraries.

# Prerequisites and limitations

* Each webhook endpoint has a unique secret key (found on the **Webhook Endpoints** page).
* Signature verification only applies to JSON payloads (XML payloads are not signed).
* You must have access to the raw request body and the `recurly-signature` header.

# Key details

Each webhook endpoint is assigned a unique secret key. The key can be found on the **Webhook Endpoints** page. The matching key will be needed for each endpoint you wish to verify.

### Using a client library

Provide the `recurly-signature` header, the secret key, and the raw request body:

```ruby
begin
  Recurly::Webhooks.verify_signature(header,
                                     ENV['WEBHOOKS_KEY'],
                                     request.body)
rescue Recurly::Errors::SignatureVerificationError => e
  puts e.message
end
```
```go
err := recurly.VerifyWebhookSignature(header, secret, body, recurly.DEFAULT_WEBHOOK_TOLERANCE)
if err != nil {
  fmt.Errorf("Verify Webhook Signature failed with: %s", err.Error())
}
```

### Manual check

The `recurly-signature` header is formatted as a Unix timestamp (in milliseconds), followed by one or more hexadecimal signatures:

```go
recurly-signature:1659641851,a8c8524a0cdd99e36b55d9fdf6c8aed2c2315dfa1a36c4961f65986ee6cf6ae9,cafe677a2e6fa177d1a73cd9a18e47533de4b8923bbe17e2e1be290717ca29c1
```

Signatures are generated via HMAC using SHA-256. When you regenerate a secret key, both the new and previous keys remain valid for 24 hours, resulting in multiple signatures in the header.

#### 1. Extract timestamp and signature(s)

Split the header value on commas. The first element is the timestamp; the remaining elements are signature(s). Any other format is invalid.

#### 2. Prepare the message

Concatenate:

* The timestamp
* A literal `.` character
* The exact raw request body

#### 3. Compute the expected signature

Compute the HMAC-SHA256 digest of the prepared message using your endpoint’s secret key.

#### 4. Compare signatures

Use a constant-time comparison to check the expected signature against each signature from the header. The notification is valid if **exactly one** matches.

Also compare the timestamp against the current time. Some clock skew is normal, but a large discrepancy could indicate a [replay attack](https://en.wikipedia.org/wiki/Replay_attack).