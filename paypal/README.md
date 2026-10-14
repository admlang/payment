# adm.payment.paypal

A PayPal client in ADM over `std.net.http`: the REST APIs (orders, payments, subscriptions,
payouts), their webhooks, and verification of IPN messages for integrations that still receive
them.

```bash
adm get admlang:adm.payment.paypal
```

```adm
use adm.payment.paypal

let pp = try paypal.connect(clientId, secret, sandbox = true)   // PAYPAL_CLIENT_ID, PAYPAL_CLIENT_SECRET
let order = try pp.orders.create(new paypal.Money(2499, "EUR"), returnURL = "https://example.com/done", cancelURL = "https://example.com/cart")
print(order.approveLink)                           // where the buyer goes
let captured = try pp.orders.capture(order.id)

let hooks = new paypal.Webhooks(pp, webhookId)
server.handle("/paypal", hooks.handler((event paypal.Event) => orders.record(event)))
```

## Tests

`adm test payment/paypal` runs against a loopback server that answers as PayPal does.
`ADM_TEST_PAYPAL=client_id:secret` adds calls to a sandbox application.
