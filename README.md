# ADM payment libraries

Official [ADM](https://github.com/admlang/adm) libraries that take payments through a provider.
Each is written in ADM over `std.net.http`, published as `adm.payment.<provider>` and signed by the
`admlang` publisher. They live outside the standard library because payment APIs are versioned
by date and change often, and a fix must ship the week the provider changes something.

| Library | Provider | What it covers |
|---|---|---|
| [`adm.payment.stripe`](stripe/) | Stripe | the core objects (customers, payment intents, checkout, subscriptions, invoices, products and prices, refunds, payouts), webhooks, Connect |
| [`adm.payment.paypal`](paypal/) | PayPal | orders and payments v2, subscriptions, payouts, refunds, disputes, webhooks, IPN verification |

## Shared shape

Each library is a `Client` over a secret key, typed objects and methods named as the provider
names them, idempotency keys on every write, pagination by `all()`, and a webhook handler for
`std.net.http` that verifies the provider's signature and hands out typed events. Amounts are
integers of the currency's minor unit with an ISO 4217 code, never floats. Every failure is one
error type per library with a `kind` (`CardDeclined`, `Invalid`, `Auth`, `RateLimited`,
`Idempotency`, `NotFound`, `Transport`, `Server`), the provider's code, the decline code when
there is one, and the request id.

## Tests

Each suite runs against a loopback server that answers as the provider does, so `adm test`
needs no account. With test-mode credentials the suite also calls the provider's sandbox:

```bash
ADM_TEST_STRIPE_KEY=sk_test_... adm test payment/stripe
ADM_TEST_PAYPAL=client_id:secret adm test payment/paypal        # a sandbox application
```
