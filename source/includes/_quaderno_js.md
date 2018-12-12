# Quaderno.js

## Introduction

This is the API reference for Quaderno.js. Use Quaderno.js’ APIs to calculate taxes, and accept payments with Stripe and Paypal.

### Including Quaderno.js

However you’re using Quaderno.js, you always begin by including the library and setting your API key. To get started, include this script on your pages — it should always be loaded directly from https://js.quaderno.io:

<div class="center-column"></div>
```html
  <script src="https://js.quaderno.io/v3/"></script>
```


### Supported browsers

Quaderno.js strives to support all recent versions of major browsers. For the sake of security and providing the best experience to the majority of customers, we do not support browsers that are no longer receiving security updates and represent a small minority of traffic.

- We support Internet Explorer and Edge per **[Microsoft's lifecycle policy](https://support.microsoft.com/en-us/gp/microsoft-internet-explorer)**.
- We support Chrome and Safari on all platforms and Firefox on desktop platforms.
- We support the Android native browser on Android 4.4 and later.
- We respond to bug reports but do not proactively test other mobile browsers.

If you have an issue with Quaderno.js on a specific browser, please **[contact us](mailto:support@quaderno.io)** so we can improve its support.


## Reference


```js
Quaderno.publishableKey = "pk_test00112233445566";
Quaderno.productId = "prod_0011223344556677";
Quaderno.gateway = "stripe";
Quadeno.firstName = "Perro";
Quaderno.lastName = "Muchacho";
Quaderno.email = "perro@muchacho.com";
Quaderno.streetLine1 = "C/Enrique Jardiel Poncela 4 ";
Quaderno.city = "Madrid";
Quaderno.postalCode = "28016";
Quaderno.region = "Madrid";
Quaderno.country = "ES";
```

When you include the Quaderno.js script in your page, a global `Quaderno` object will be available with the following attributes:

Attribute          | Description
-------------------|-----------------------------------------------------------------------------------------------------------
`publishableKey`   | Your Quaderno publishable key.
`productId`        | The Quaderno code of the product you want to sell.
`gateway`          | The payment gateway to process the transaction. Supported values are: `stripe` and `paypal. Default is stripe.
`firstName`        |
`lastName`         |
`email`            |
`companyName`      |
`streetLine1`      |
`streetLine2`      |
`city`             |
`postalCode`       |
`region`           |
`country`          | ISO 3166-1 alpha-2
`businessNumber`   | A valid business number. Supported values are: EU VAT numbers, ABN, and NZBN.
`coupon`           | A valid Stripe subscription percent off discount coupon mapped in your [Quaderno account](https://quadernoapp.com/coupons).

### init( publishableKey, productId [, options] )

```js
  Quaderno.init(publishableKey, productId, {
    gateway: "stripe",
    firstName: "Marcial",
    "LastName": "Ruiz Escribano"
  }).then(function(response){
    console.log(response.gateway, response.product);
  }).catch(function(error){
    console.log(error.description, error.messages);
  });
```

***Async request***. Use this method to initialize the global object `Quaderno`.

It accepts an optional `options`  object. Available options are documented below:

Attribute          | Description
-------------------|-----------------------------------------------------------------------------------------------------------
`gateway`          | The payment gateway to process the transaction. Supported values are: `stripe` and `paypal`. Default is stripe.
`firstName`        |
`lastName`         |
`email`            |
`companyName`      |
`streetLine1`      |
`streetLine2`      |
`city`             |
`postalCode`       |
`region`           |
`country`          | ISO 3166-1 alpha-2
`businessNumber`   | A valid business number. Supported values are: EU VAT numbers, ABN, and NZBN.

This method returns a `Promise` which resolves with a `result` object. This object has either:

- **result.gateway**: a `gateway` object with general info about the chosen payment processor.
- **result.product**: a `product` object.

If there's any issue, this method returns a `Promise` which rejects with an `error` object. This object has either:

- **error.description**: A full description of the error.
- **error.messages**: If present, it indicates the affected attribute and an specific error (ie. `{ email: "invalid", businessNumber: "invalid" }`).


### initForm( formId )

```js
  Quaderno.initForm("#formId").then(function(response){
    console.log(response.gateway, response.product);
  }).catch(function(error){
    console.log(error.description, error.messages);
  });
```

***Async request***. This method also initializes the global object `Quaderno` from a form object.

This method returns a `Promise` which resolves with a `result` object. This object has either:

- **result.gateway**: a `gateway` object with general info about the chosen payment processor.
- **result.product**: a `product` object.

If there's any issue, this method returns a `Promise` which rejects with an `error` object. This object has either:

- **error.description**: A full description of the error.
- **error.messages**: A brief description if each invalid attribute.

### calculateTaxes()

```js
  Quaderno.taxesCalculate().then(function(taxObject){
    console.log(taxObject.name, taxObject.rate, taxObject.country); // ie. "VAT", 20, "GB"
  })
```

***Async request***. Calculates the taxes based on the billing data from the `Quaderno` object.

This method returns a `Promise` which resolves with a `tax` object.

### calculatePrice()

```js
  priceObject = Quaderno.calculatePrice();

  priceObject.subtotal;
  priceObject.taxes;
```

Calculates the final price of the product, tax inclusive.

This method returns a `price` object with the following attributes:

- subtotal: the price without taxes and discounts.
- taxes: the taxes amount.
- total: the final amount.
- discountedAmount: The amount minus the applied discount

### createTransaction( cardToken )

```js
  Quaderno.createTransaction(cardToken).then(function(transactionDetails){
    console.log(transactionDetails)
  }).catch(function(error){
    // Show the error message if the transaction couldn't be created
    alert(error.description);
  })
```

***Async request***. Creates either a subscription or a one-off charge if the payment gateway is Stripe.
This method returns a `Promise` which resolves with an object with information related to the recently created transaction.


### redirectToPayPal()

```js
  Quaderno.redirectToPayPal().catch(function(error){
    // Show the error message if the transaction couldn't be created
    alert(error.description);
  })
```

***Async request.*** Automatically redirect your customer to the PayPal checkout page.

### destroy()
```js
  Quaderno.destroy();
```

Remove event listeners from the DOM elements. It might be useful if you have single-page apps and have tied the Quaderno object to a form via `initForm` or the [simple initialization](#quaderno-js-how-to-initialize-quaderno-js-simple-automatic-init).

### getGateway()
```js
  Quaderno.gateway = "stripe";

  Quaderno.getGateway().then(function(gateway){
    // Stripe publishable key needed for card tokenization
    console.log(gateway.gatewayKey);
  });
```

***Async request***. Retrieves the payment gateway public key. Returns a `gateway` object in the resolve function.

### getProduct()
```js
  Quaderno.productId = "productId";

  Quaderno.getProduct().then(function(product){
    console.log(product.name);
  });
```

***Async request***. Retrieve the product info identified by `productId`. Returns a `product` object in the resolve function.

### getTaxes()
```js
  taxes = Quaderno.getTaxes();

  taxes.name;
  taxes.rate;
  taxe.country;
```

Reads the taxes cache and returns a `tax` object.

### reload()
```js
  Quaderno.gateway = "stripe";
  Quaderno.productId = "anotherProduct";

  Quaderno.reload().then(function(response){
    console.log(response.gateway, response.product);
  }).catch(function(error){
    console.log(error.description, error.messages);
  });
```

***Async request***. Reloads the object `Quaderno` if any notable configuration options (mainly `publishableKey` and `productId`) have been modified.

This method returns a `Promise` with the same behavior as `Quaderno.init`

### validateBusinessNumber()
```js
  Quaderno.businessNumber = "GB1234567";
  Quaderno.country = "GB";

  Quaderno.validateBusinessNumber().then(function(response){
    console.log(response.valid);
  }).catch(function(error){
    console.log(error.descriptions);
  });
```

***Async request***. This method validates the customer's business number. Currently Quaderno validates EU VAT numbers, ABN, and NZBN.

This method returns a `result` object with the following attribute:

- **result.valid**: true, false or null.

### The Tax object

This object is passed by the resolve function of the `Promise` returned by the `Quaderno.calculateTaxes` method or by invoking `Quaderno.readTaxes`.

Attribute          | Description
-------------------|-----------------------------------------------------------------------------------------------------------
`country`          | A valid ISO 3166-1 alpha-2 code.
`region`           |
`county`           |
`name`             |
`rate`             |
`extraName`        |
`extraRate`        |
`taxClass`         | The tax class of the transaction. Can be one of the following: `standard`, `eservice` or `ebook`.
`invalidCoupon`    | Boolean. If a coupon was passed, indicates wether it was valid or not.
`discountedAmount` | The original amount minus discounts.
`percentOff`       | The discount rate applied.


### The Gateway object

Contains relevant information about the payment gateway. Currently, it only works for Stripe.  It's returned by the resolve function of `Quaderno.init`, `Quaderno.initForm` (in conjunction with the product object) or `Quaderno.getGateway`. It contains just an attribute:


Attribute          | Description
-------------------|-----------------------------------------------------------------------------------------------------------
`gatewayKey`       | Your Stripe publishable key.


### The Product object

This object contains the relevant information related to the productID you've set. It's returned by the resolve function of `Quaderno.init`, `Quaderno.initForm` (in conjunction with the gateway object) or `Quaderno.getProduct`.

Attribute          | Description
-------------------|-----------------------------------------------------------------------------------------------------------
`name`             | A valid ISO 3166-1 alpha-2 code.
`image`            |
`gateway`          | The payment gateway to process the transaction. Supported values are: `stripe` and `paypal`. Default is `stripe`.
`unitCost`         |
`currency`         |
`kind`             | Indicate if the product is either a `subscription` or a `one-off`.
`extraRate`        |
`taxClass`         | The tax class of the transaction. Can be one of the following: `standard`, `eservice` or `ebook`.
`taxType`          | Indicates if the tax is included or excluded from the unitCost


##How to initialize Quaderno.js

### Simple automatic init

Automatically loads quaderno.js if the form has the `quaderno-payment-form` ID and a valid `data-key` and `data-product`.

- ***data-key***: Your Quaderno public key
- ***data-product***: The product code you've registered in Quaderno

The input fields will be automatically detected as long as they have the `data-quaderno` with their definition:

- ***firstName*** (mandatory)
- ***lastName***
- ***streetLine1***
- ***streetLine2***
- ***city***
- ***region***
- ***postalCode***
- ***country*** (mandatory)
- ***email***
- ***businessNumber***
- ***taxId***

<div class="center-column"></div>
```html
    <head>
      <script src="https://js.quaderno.io/v3/"></script>
    </head>

    <body>
      <form id="quaderno-payment-form" data-publishable-key="pk_xxxxxxxx" data-product="prod_xxxxxxxx">
        <input data-quaderno="firstName" />
        <input data-quaderno="lastName" />
        <select data-quaderno="country"> <!--options … --> </select>
        <!-- … -->
      </form>
    </body>
```


### Deferred automatic init

If you don't want the form to load automatically, you can load it later by not using the ***"quaderno-payment-form"*** id, and calling the `initForm` method whenever you want.

<div class="center-column"></div>

```html
    <head>
      <script src="https://js.quaderno.io/v3/"></script>
    </head>

    <body>
      <form id="payment-form" data-key="pk_xxxxxxxx" data-product="prod_xxxxxxxx">
        <input data-quaderno="firstName" />
        <input data-quaderno="lastName" />
        <!-- … -->
      </form>

      <script>
        Quaderno.initForm('#payment-form').then(function(response) {
          console.log(response.gateway, response.product);
        }).catch(function(error){
          console.log(error.description, error.messages);
        });
      </script>
    </body>
```

### Custom init

Futhermore, you can also dissociate Quaderno from the DOM by setting the Quaderno object properties by yourself. This means you need to explicitly set the minimum mandatory configuration options during the `init` call.

- publishableKey: Your Quaderno publishable key.
- productId: The ID of the product registered in Quaderno.

<div class="center-column"></div>
```html
    <label for="country">
      <span>Country</span>
      <input id="country" name="country" value="ES" />
    </label>

    <label for="postal-code">
      <span>Postal code</span>
      <input id="postal-code" name="postal-code" value="35007" />
    </label>

    <script>
      Quaderno.init(publishableKey, productId
        {
          gateway: 'stripe',
          country: $('#country').val(),
          postalCode: $('#postal-code').val(),
        },
      ).then(function(response) {
        console.log(response.gateway, response.product);
      }).catch(function(error) {
        console.log(error.description, error.messages);
      });
    </script>
```

By using the custom init, you can also programmatically swap configuration options by setting the Quaderno attributes and reloading it. In this example we are swapping the product and reloading so we can get the new product information:

<div class="center-column"></div>
```javascript
    Quaderno.productId = 'newProductId';

    // As the new product might have a different price and/or taxes configurations, we must call "reload" to retrieve the missing information
    Quaderno.reload().then(function(response) {
      console.log(response.gateway, response.product);
    }).catch(function(error) {
      console.log(error.description, error.messages);
    });
```

Usually you want to `reload` the checkout when one of those values has changed:

- gateway
- productId


## How to use Quaderno.js to calculate taxes

> In this example, we are going to handle updates on the relevant tax-related fields to force taxes calculations and show it in a `<span>`:

```html
  <label for="country">
    Country
    <select id="country">
      <option value="US" selected>United States</option>
      <option value="GB">United Kingdom</option>
      <option value="DE">Germany</option>
      <option value="FR">France</option>
      <option value="IT">Italy</option>
      <option value="ES">Spain</option>
      <option value="CA">Canada</option>
      <option value="AU">Australia</option>
      …
    </select>
  </label>

  <label for="postal-code">
    Postal code
    <input id="postal-code">
  </label>

  <label for="business-number">
    <input id="business-number">
  </label>

  Taxes: <span id="tax-rate"></span>

  <script>
    function reloadTaxes(){
      Quaderno.taxesCalculate().then(function(taxObject){
        document.querySelector('tax-rate').innerHTML = taxObject.name + " " + taxObject.rate + "%"; // ie.: VAT 20%
      })
    }

    // Handle the change events on the relevant user-inputs
    document.querySelector('#country').addEventListener('change', function () {
      Quaderno.country = this.value
      reloadTaxes()
    })

    document.querySelector('#postal-code').addEventListener('change', function () {
      Quaderno.postalCode = this.value
      reloadTaxes()
    })

    document.querySelector('#business-number').addEventListener('change', function () {
      Quaderno.businessNumber = this.value
      reloadTaxes()
    })
  </script>
```

You can use Quaderno.js to calculate taxes by invoking the method `Quaderno.calculateTaxes`.

If you are showing real time taxes calculations it's usually a good idea to force taxes calculations when the country, postal code or business number has changed.


## How to use Quaderno.js to charge your customers

All the methods described in this page needs at least two mandatory attributes:

- Your Quaderno [publishable key](https://quadernoapp.com/settings/api).
- The [ID of the product](https://quadernoapp.com/items) you want to sell.

Whether your transaction is a recurring charge or a one-off charge is determined by the way you've registered the product in Quaderno. Choose "subscription" if you want a recurring charge or "one-off" if you want a one-off charge.

### With simple automatic init or deferred automatic init

The simple automatic init is the most simple way to integrate Quaderno.js with your page. You just need to set some attributes on your checkout form and Quaderno.js will automatically link to it  in order to show live taxes calculations and grab the necessary data from your customer input. Then you will only need to tokenize the card and handle the success or the errors.

The deferred automatic init is almost the same but it let you decides when and to which form should Quaderno.js be initialized by providing the function `Quaderno.initForm` (more information [here](#quaderno-js-reference-initform-formid)).

Either way, in order to create a transaction first, grab the code of your subscription product and paste it as the `data-product-id` in your form. For example:

<div class="center-column"></div>
```html
  <div id="payment-errors"></div>

  <form id="quaderno-payment-form"
    data-publishable-key="pk_test_publishable_key"
    data-product-id="prod_code"
    data-gateway="stripe">
    <label>
      <span> First name </span>
      <input data-quaderno="firstName">
    </label>

    <label>
      <span> Last name </span>
      <input data-quaderno="lastName">
    </label>

    <label>
      <span> Country </span>
      <input data-quaderno="country">
    </label>

    <label>
      <span> Postal code </span>
      <input data-quaderno="postalCode">
    </label>

    …

    <div id="card-element"></div>
    <button type="submit" id="submit-button">Pay now</button>
  </form>
```


When the customer submits the form, in case you are using a card payment method such as Stripe,  you'll need to first tokenize the card and pass the resulting token to `Quaderno.createTransaction`. In the following example we are loading our core after the page has been properly loaded in order to tokenize the card:

<div class="center-column"></div>
```html
  <script>
    window.addEventListener('load', function(){
      var form = document.getElementById('quaderno-payment-form');
      var errorPanel = document.getElementById('payment-errors');

      // Setup the form handlers after Quaderno.js has been initialized
      form.addEventListener('initialized.Quaderno', function() {
        var stripe = Stripe(Quaderno.gatewayKeys.stripe);
        var elements = stripe.elements();
        var card = elements.create('card', { hidePostalCode: true });
        card.mount('#card-element');

        // Handle submit event on the form
        form.addEventListener('submit', function(event) {
          event.preventDefault();

          var button = document.getElementById('submit-button');
          // Disable the submit button to prevent multiple clicks
          button.disabled = true;

          // Tokenize the card
          stripe.createToken(card).then(function(result) {
            if (result.error) {
              // Inform the customer that there was an error.
              errorPanel.textContent = result.error.message;
              button.disabled = false;
            } else {
              // If the tokenization was a success then send the token to your server.
              Quaderno.createTransaction(result.token.id).then(function(response){
                // Submit the form to your backend if everything was correct
                form.submit();
              }).catch(function(error){
                // Show the error message if the transaction couldn't be created
                errorPanel.textContent = error.description;
                button.disabled = false;
              })
            }
          });
        })
      })
    })
  </script>
```

Please note the relevant parts in the snippet from above, first we call `stripe.createToken` in order to tokenize the card, as it's a JS promise, we expect the result inside the resolve function. Only if there isn't any errors we attempt to create the transaction in Stripe by calling `Quaderno.createTransaction`.

If you use PayPal then it's much simpler as it doesn't require any card tokenization, you just need to redirect your customer to PayPal after they submit the form. As a downside as the customer is redirected outside of our checkout, we are no longer able to return the PayPal in the promise:

<div class="center-column"></div>
```html
  <script>
    window.addEventListener('load', function(){
      var form = document.getElementById('quaderno-payment-form');
      var errorPanel = document.getElementById('payment-errors');

      // Setup the form handlers after Quaderno.js has been initialized
      form.addEventListener('initialized.Quaderno', function() {
          // Handle submit event on the form
        form.addEventListener('submit', function(event) {
          event.preventDefault();

          var button = document.getElementById('submit-button');
          // Disable the submit button to prevent multiple clicks
          button.disabled = true;

          // Redirect your customer to PayPal
          Quaderno.redirectToPayPal().catch(function(error){
            // Handle possible errors
            errorPanel.textContent = error.description;
            button.disabled = false;
          });
        })
      })
    })
  </script>
```

In this case the relevant function is `Quaderno.redirectToPayPal`, in case of success it automatically redirects the customer to the PayPal payment page, otherwise we will catch the error in the reject block and show it to the customer.

### With custom init
```html
  <h1>James Tiberius Kirk</h1>
  <span id="country">CH</span>
  <label>
    Postal code
    <input id="postal-code" />
  </label>

  <script>
    var publishableKey = 'pk_test_0123456789';
    var productId = 'prod_0123456789';

    Quaderno.init(publishableKey, productId
      {
        gateway: 'stripe',
        firstName: $('h1').html(),
        streetLine1: 'USS Enterprise',
        country: $('#country').val(),
      },
    ).then(function(response) {
      console.log(response.gateway, response.product);
    }).catch(function(error) {
      console.log(error.description, error.messages);
    });
  </script>
```

The custom init provides you all the flexibility you need in order to integrate Quaderno.js with your system. Unlike the automatic init or the deferred automatic init, it doesn't require the rigid schema of `data` attributes, it doesn't even require to be linked to any element from the DOM if you don't want to!

The way it operates is very simple, you just need the global Quaderno object and assign your customer attributes to its accepted fields.

First we need to initialize the `Quaderno` object:

<aside class="notice"> Tip: If you need to swap your product after an init, remember to reload Quaderno by invoking <a href="#quaderno-js-reference-reload">Quaderno.reload()</a>
</aside>

After you have the Quaderno object correctly configured, all you need is to tokenize the card (if you are using a card payment method) and create the transaction via `Quaderno.createTransaction` :

<div class="center-column"></div>
```html
  <h1>James Tiberius Kirk</h1>
  <span id="country">CH</span>
  <label>
    Postal code
    <input id="postal-code" />
  </label>

  <!-- Card container for Stripe elements. You can get more information here: https://stripe.com/docs/stripe-js-->
  <div id="card-container"></div>

  <button id="pay-button">Pay now!</button>

  // Tokenize the card with Stripe and create the transaction.
  <script>
    var publishableKey = 'pk_test_0123456789';
    var productId = 'prod_0123456789';
    var stripe = null;

    // Initialize the Quaderno object
    Quaderno.init(publishableKey, productId
      {
        gateway: 'stripe',
        firstName: $('h1').html(),
        streetLine1: 'USS Enterprise',
        country: $('#country').html(),
        postalCode: $('#postal-code').val(),
      },
    ).then(function(response) {
      // After a successful initialization we can mount Stripe elements
      stripe = Stripe(Quaderno.gatewayKeys.stripe);
      elements = stripe.elements();
      card = elements.create('card', { hidePostalCode: true });
      card.mount('#card-element');

      // Tokenize the card and create the transaction when the customer clicks the button
      $('#pay-button').on('click', function(){
        // Disable the button in order to prevent multiple clicks
        $(this).attr('disabled', 'disabled');

        // Tokenize the card
        stripe.createToken(card).then(function(result) {
          if (result.error) {
            //Tokenization error: inform the customer that there was an error.
            alert(result.error.message);
            button.disabled = false
          } else {
            // Just an example of updating the postal code
            Quaderno.postalCode = $('#postal-code').val();

            // Tokenization success: create the transaction.
            Quaderno.createTransaction(result.token.id).then(function(response){
              // Submit the form to your backend if everything was correct
              form.submit();
            }).catch(function(error){
              // Show the error message if the transaction couldn't be created
              alert(error.description);
              button.disabled = false;
            })
          }
        });
      })
    }).catch(function(error) {
      console.log(error.description, error.messages);
    });
  </script>
```

If you are using PayPal as  the configured gateway, rather than using `Quaderno.createTransaction`, you need to call `Quaderno.redirectToPayPal`:

<div class="center-column"></div>
```html
  <h1>James Tiberius Kirk</h1>
  <span id="country">CH</span>
  <label>
    Postal code
    <input id="postal-code" />
  </label>

  <!-- Card container for Stripe elements. You can get more information here: https://stripe.com/docs/stripe-js-->
  <div id="card-container"></div>

  <button id="pay-button">Pay now!</button>

  // Tokenize the card with Stripe and create the transaction.
  <script>
    var publishableKey = 'pk_test_0123456789';
    var productId = 'prod_0123456789';

    // Initialize the Quaderno object
    Quaderno.init(publishableKey, productId
      {
        gateway: 'stripe',
        firstName: $('h1').html(),
        streetLine1: 'USS Enterprise',
        country: $('#country').html(),
        postalCode: $('#postal-code').val(),
      },
    ).then(function(response) {
      // Redirect to PayPal when the customer clicks the button
      $('#pay-button').on('click', function(){
        // Disable the button in order to prevent multiple clicks
      var button = $(this).attr('disabled', 'disabled');
      // Just an example of updating the postal code
      Quaderno.postalCode = $('#postal-code').val();

      // Redirect your customer to PayPal
      Quaderno.redirectToPayPal().catch(function(error){
        // Handle possible errors
        alert(error.description);
        button.disabled = false;
      });
    }).catch(function(error) {
      console.log(error.description, error.messages);
    });
  </script>
```
