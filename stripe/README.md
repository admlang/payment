# adm.payment.stripe

A Stripe client in ADM over `std.net.http`: the core objects of a business that charges cards
and runs subscriptions, and the webhooks that tell it what happened.

```bash
adm get admlang:adm.payment.stripe
```

```adm
use adm.payment.stripe

let st = new stripe.Client(secretKey)             // STRIPE_SECRET_KEY when left out
let customer = try st.customers.create(email = "ana@example.net", name = "Ana")
let intent = try st.paymentIntents.create(2499, "eur", customer = customer.id, idempotencyKey = orderId)
print(intent.clientSecret)                        // handed to Stripe.js in the browser

let session = try st.checkout.sessions.create(mode = stripe.CheckoutMode.Subscription,
	price = "price_123", successURL = "https://example.com/done", cancelURL = "https://example.com/cart")

// the webhook endpoint
let hooks = new stripe.Webhooks(endpointSecret)
server.handle("/stripe", hooks.handler((event stripe.Event) => orders.record(event)))
```

## Tests

`adm test payment/stripe` runs against a loopback server that answers as Stripe does.
`ADM_TEST_STRIPE_KEY=sk_test_...` adds calls to Stripe's test mode with its test cards.
