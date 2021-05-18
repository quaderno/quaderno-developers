# Billing

Billing is the simplest way to give your customers access to all their receipts & billing history. They can easily download past receipts and update their billing data by themselves. Improve your billing support with a single line of code.

<div style="width: 60%; margin: 0 auto;">
  <script
    src="https://billing.quaderno.io/billing.js" class="quaderno-billing-button"
    data-key="pk_live_-8TWhJcCjnZeDyceDza7"
    data-customer-id="92041d676b3cfc7fe1cb2457d319f534118ba098"
    data-label="View billing">
  </script>
</div>

## Reference

When you include the Quaderno Billing script in your page, a global `QuadernoBilling` object will be available with the following methods:

<aside class="warning">
Please keep in mind these methods are oriented to the <a href="#quaderno-billing-integration-custom-integration">custom integration</a>. If you are using the <a href="#quaderno-billing-integration-simple-integration">simple integration</a> we recommend not using them.
</aside>

### configure(options = {})

```js
QuadernoBilling.configure({
  key: 'QUADERNO_PUBLIC_KEY',
  customer_id: 'CUSTOMER_SECURE_ID_OR_PAYMENT_GATEWAY_ID',
  editable: true
})
```

Configures the basic checkout options that cannot be changed (unless `close()` is invoked). Accepts the following options:

Option                   |Mandatory                                             | Description
-------------------------|------------------------------------------------------|----------------------
key                      |Yes                                                   | Your Quaderno publishable key.
customer_id              |Yes                                                   | The customer secure ID. You can also use their Stripe, Braintree, Paymill, or PayPal IDs if the customer was created via those gateways
editable                 |No                                                    | Select wether or not your user can edit the invoices he opens

### open()

```js
QuadernoBilling.open();
```
Programmatically opens the billing modal.

### close()

```js
QuadernoBilling.close();
```
Programmatically close the billing modal form and remove it from your DOM. After this, you can call `configure` again if you want to set up basic configuration options again or `open()` to reopen the billing modal.

##Integration
You can integrate Billing in as little as a single line of client-side code. As we release new features, we’ll automatically roll them out to your existing Billing integration, so that you will a be using our latest technology without needing to change a thing.

###Simple integration
```html
<script
  src="https://billing.quaderno.io/billing.js" class="quaderno-billing-button"
  data-key="YOUR_QUADERNO_PUBLISHABLE_KEY"
  data-customer-id="CUSTOMER_SECURE_ID_OR_PAYMENT_GATEWAY_ID"
  data-label="View billing">
</script>
```

Just add this `<script>` tag in your HTML page to render the billing button. When users click the button we’ll show them their past receipts.


Configuration options:

Option             | Required | Description
-------------------|----------|-------------------------------------------------------------------
data-key           | Yes      | Your publishable key.
data-customer-id   | Yes      | The customer secure ID. You can also use their Stripe, Braintree, Paymill, or PayPal IDs if the customer was created via those gateways.
data-label         | No       | The text to be shown on the default green button.
data-editable      | No       |Allow your users to edit their billing data. The default is  true.

###Custom integration

```html
<head>
  <script src="https://billing.quaderno.io/billing.js"></script>
</head>

<button id="billing">Check your invoices</button>

<script>
  QuadernoBilling.configure({
    key: 'QUADERNO_PUBLIC_KEY',
    customer_id: 'CUSTOMER_SECURE_ID_OR_PAYMENT_GATEWAY_ID',
    editable: true
  })

  $('#billing').on('click', function(e) {
    e.preventDefault();
    QuadernoBilling.open();
  });
</script>
```
The custom integration lets you create a custom button and bind it to Quaderno Billing. This permits any HTML element or JavaScript event to start launch the modal.
When your page loads, you should create a handler object using `QuadernoBilling.configure()`. You can call `open()` on the handler in response to any event.
If you need to reload the modal-for example, when navigation occurs in a single-page application—you can call  close() on the handler.
The  key and customer_id parameter must be passed to `configure()`. Any other options can be passed to either `configure()` or `open()`.
