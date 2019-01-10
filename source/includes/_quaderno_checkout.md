# Quaderno Checkout

## Introduction

Checkout is the best billing flow, on web and mobile. Checkout builds on top of Quaderno.js to provide your users with a streamlined, mobile-ready payment experience that is constantly improving.

Try the demo below with this test card number: `4111 1111 1111 1111`

<div style="width: 60%; margin: 0 auto;">
  <form action="http://quadernoapp.com/login" method="POST">
    <script src="https://checkout.quaderno.io/v3/checkout.js" class="quaderno-button"
    data-company="false"
    data-publishable-key="pk_test_LNpesCnUBN6Ax__xbmc4"
    data-card-gateway="stripe"
    data-paypal="true"
    data-product-id='prod_61ffa845b4a0b8'
    data-locale="en"
    data-label="Demo"
    data-billing-address="true"
    data-panel-label="Pay {{amount}} now!"
    ></script>
  </form>
</div>

## Reference

When you include the Quaderno Checkout script in your page, a global `QuadernoCheckout` object will be available with the following methods:

<aside class="warning">
Please keep in mind these methods are oriented to the <a href="#quaderno-checkout-integration-custom-integration">custom integration</a>. If you are using the <a href="#quaderno-checkout-integration-simple-integration">simple integration</a> we recommend not using them.
</aside>

### configure(options = {})

```js
QuadernoCheckout.configure({
  publishableKey: 'pk_test_00112233445566778899',
  paypal: true,
  cardGateway: 'stripe',
  locale: 'es',
  callback: function(e){
    // Transaction successfully created
    console.log(e)
  }
});
```

Configures the basic checkout options that cannot be changed (unless `close()` is invoked). Accepts the following options:

Option                   |Mandatory                                             | Description
-------------------------|------------------------------------------------------|-----------------------------
publishableKey           |Yes                                                   |Your Quaderno publishable key.
charge                   |No                                                    |A  JWT with extra information about the transaction.
cardGateway              |No                                                    |String (only `stripe`). Specify if you want to activate the Stripe card inputs
paypal                   |No                                                    |Boolean. Specify if you want to activate PayPal as payment method
color                    |No                                                    |The RGB color for the Checkout interface. The default is #4C7800.
locale                   |No                                                    |Localize the Checkout interface (2-letter  ISO code). The default is auto`.
callback                 |No (Recommendable)                                    |A function to handle the response details after the transaction for a card payment has been made.
tosUrl                   | No (Yes if privacyUrl is present)                    |An url pointing to a page with your Terms of Service
privacyUrl               | No (Yes if tosUrl is present)                        |An url pointing to a page with your privacy policy
### open([options = {}])

```js
QuadernoCheckout.open({
  redirectUrl: 'https://your-webpage.com/thank-you',
  discount: true,
  firstName: 'Steve',
  lastName: 'Rogers',
  email: 'the@nomad.com',
  country: 'US',
  postalCode: '11201',
  productId: '123456',
  billingAddress: true
});
```
Programmatically opens the checkout with the options passed. The parameters supported by the `open()` method are the following:


Option                   |Mandatory                                             | Description
-------------------------|------------------------------------------------------|--------------------------------------
redirectUrl              |Yes (only if `paypal` has been set to `true`)         |URL to which redirect your customers after finishing the checkout process in PayPal.
productId                |Yes                                                   |The ID of the product you want to sell.
charge                   |No                                                    |A  JWT with extra information about the transaction.
billingAddress           |No                                                    |The checkout form ask for the customer’s full billing address and tax ID. The default is false.
panelLabel               |No                                                    |The label of the payment button in the Checkout (e.g. “Subscribe now”, “Pay {{amount}}”, etc.). If you include  {{amount}}, it will be replaced by the provided amount. Otherwise, the amount will be appended to the end of your label.
company                  |No                                                    |Boolean. Toggle the company name input. Default is false
firstName                |No                                                    |Your customer’s first name, to pre-fill the checkout form.
lastName                 |No                                                    |Your customer’s last name, to pre-fill the checkout form.
companyName              |No                                                    |Your customer’s company name, to pre-fill the checkout form.
email                    |No                                                    |Your customer’s email address, to pre-fill the checkout form.
country                  |No                                                    |2-letter ISO code. Set it if you want to preload the customer's country.
discount                 |No                                                    |Customers can enter a coupon code (only works for Stripe subscriptions). The default is `false`.

### close()

```js
QuadernoCheckout.close();
```
Programmatically close the checkout form and remove it from your DOM. After this, you can call `configure` again if you want to set up basic configuration options again or `open()` to reopen the checkout form.


##Integration

Depending on your needs, you can either integrate Checkout as just a copy and paste snippet with almost zero coding with the simple integration or you can get more flexibility by using the custom integration.

###Simple integration
```html
<form action="/response-handler" method="POST">
  <script src="https://checkout.quaderno.io/v3/checkout.js" class="quaderno-button"
  data-company="false"
  data-publishable-key="pk_test_LNpesCnUBN6Ax__xbmc4"
  data-card-gateway="stripe"
  data-paypal="true"
  data-product-id='prod_61ffa845b4a0b8'
  data-locale="en"
  data-label="A demo button"
  data-billing-address="true"
  data-panel-label="Pay {{amount}} now!"
  ></script>
</form>
```

You can integrate Checkout in as little as a single line of client-side code. As we release new features, we’ll automatically roll them out to your existing Checkout integration, so that you will always be using our latest technology without needing to change a thing. Just add this  `<script>` tag inside your checkout `<form>` to render the payment button. When a user clicks the button and completes the payment, we will create a charge or a subscription and submit your form with the transaction details along with any other `<input>` fields in your form.

Once the transction is complete, a new `transactionDetails` input will be added to the form and it will be submitted to your form’s action endpoint, along with any other `<input>` in the form.

The `transactionDetails` value is a JWT string encoded with your Quaderno private key that contains the transaction details. The  `transactionDetails` input contains the following data:

<div class="center-column"></div>
```js
{
  customer: 'CUSTOMER_ID',
  transaction: 'TRANSACTION_ID',
  type: 'subscription or charge',
  gateway: 'stripe',
  product_id: 'PRODUCT_CODE'
  iat: UNIX timestamp
}
```


You can use this data to sync the transaction in your back-end.

These are the accepted `data` attributes for configuring checkout in the simple integration:

Option                        |Mandatory                                             | Description
------------------------------|------------------------------------------------------|-----------------------------
data-publishable-key          |Yes                                                   |Your Quaderno publishable key.
data-product-id               |Yes                                                   |The ID of the product you want to sell.
data-charge                   |No                                                    |A  JWT with extra information about the transaction.
data-card-gateway             |No                                                    |String (only `stripe`). Specify if you want to activate the Stripe card inputs
data-paypal                   |No                                                    |Boolean. Specify if you want to activate PayPal as payment method
data-billing-address          |No                                                    |The checkout form ask for the customer’s full billing address and tax ID. The default is false.
data-panel-label              |No                                                    |The label of the payment button in the Checkout (e.g. “Subscribe now”, “Pay {{amount}}”, etc.). If you include  {{amount}}, it will be replaced by the provided amount. Otherwise, the amount will be appended to the end of your label. Only works for Stripe.
data-tos-url                  | No (Yes if data-privacy-url is present)              |An url pointing to a page with your Terms of Service
data-privacy-url              | No (Yes if data-tos-url is present)                  |An url pointing to a page with your privacy policy
data-company                  |No                                                    |Boolean. Toggle the company name input. Default is false
data-first-name               |No                                                    |Your customer’s first name, to pre-fill the checkout form.
data-last-name                |No                                                    |Your customer’s last name, to pre-fill the checkout form.
data-company-name             |No                                                    |Your customer’s company name, to pre-fill the checkout form.
data-email                    |No                                                    |Your customer’s email address, to pre-fill the checkout form.
data-country                  |No                                                    |2-letter ISO code. Set it if you want to preload the customer's country.
data-label                    |No                                                    |The text to be shown on the button the customer presses to show the checkout form.
data-color                    |No                                                    |The RGB color for the Checkout interface. The default is #4C7800.
data-locale                   |No                                                    |Localize the Checkout interface (2-letter  ISO code). The default is EN.
data-coupon                   |No                                                    |Customers can enter a coupon code (only works for Stripe subscriptions). The default is false.

###Custom Integration

The custom integration lets you create a custom DOM element and bind it to the Quaderno Checkout. This permits any HTML element or JavaScript event to start a Checkout payment.

When your page loads, you should create a handler object using  `QuadernoCheckout.configure()`. You can call `open()` on the handler in response to any event.

If you need to abort the Checkout process—for example, when navigation occurs in a single-page application—you can call  `close()` on the QuadernoCheckout global object.

The `publishableKey`, `cardGateway`, `paypal`, `locale`, `color` and `callback` parameter must be passed to `configure()`. Any other options should be passed to `open()`.

<head>
  <script src="https://checkout.quaderno.io/v3/checkout.js"></script>
</head>

<div class="center-column"></div>
```html
<button id="customButton">Pay with card</button>

<script>
  QuadernoCheckout.configure({
    publishableKey: 'pk_test_LNpesCnUBN6Ax__xbmc4',
    paypal: true,
    cardGateway: 'stripe',
    locale: 'es',
    callback: function(e){
      console.log(e);
    }
  });

  $('#customButton').on('click', function(e) {
    QuadernoCheckout.open({
      redirectUrl: 'https://your-webpage.com/thank-you',
      discount: true,
      firstName: 'Steve',
      lastName: 'Rogers',
      email: 'the@nomad.com',
      country: 'US',
      postalCode: '11201',
      productId: '123456',
      billingAddress: true
    })
  });

</script>
```


In case of successful payment, the  callback set in the handler will be invoked. It should accept an argument which contains a JS object with two methods: `description` and `details`.

The  description should contain a brief message about the transaction , while the `details` contain a JWT with a the transaction details such as the customer ID or the transaction ID (see the Response Parameters section above for more details).

<div class="center-column"></div>
```js
{
  address: "",
  businessNumber: "",
  companyName: "",
  country: "ES",
  description: "Charge successfully created.",
  details: "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJnYXRld2F5Ijoic3RyaXBlIiwidHlwZSI6ImNoYXJnZSIsImN1c3RvbWVyIjoiY3VzX0R4eU5SQ3djUXRYeVVwIiwiZW1haWwiOiJkYWNhc2Nvc0Bvay5jb20iLCJ0cmFuc2FjdGlvbiI6ImNoXzFEZ1ZvWkVqVkh2SU5LbGNWZVJkQWtqSSIsInByb2R1Y3RfaWQiOiIxMjM0NTYiLCJ0YXhfbmFtZSI6IklWQSIsInRheF9yYXRlIjoyMS4wLCJleHRyYV90YXhfbmFtZSI6IklSUEYiLCJleHRyYV90YXhfcmF0ZSI6LTE5LjAsImlhdCI6MTU0NDYxMzgyNH0.LgE7i_PHDVGH881HforhADlS2GbjlrXoDD7Pq4TC_5Y",
  email: "magic@taco.com",
  firstName: "Super",
  lastName: "Nacho",
  postalCode: "123456",
  taxId: "0011223344",
  type: "QCheckout.success"
}
```