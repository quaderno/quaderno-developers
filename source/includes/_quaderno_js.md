# Quaderno.js

## Introduction

This is the API reference for Quaderno.js. Use Quaderno.js’ APIs to calculate taxes in your forms in real time.

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
Quaderno.city = "Pasadena";
Quaderno.postalCode = "91104";
Quaderno.region = "CA";
Quaderno.country = "US";
```

When you include the Quaderno.js script in your page, a global `Quaderno` object will be available with the following attributes:

Attribute          | Description
-------------------|-----------------------------------------------------------------------------------------------------------
`publishableKey`   | Your Quaderno publishable key.
`productId`        | The Quaderno code of the product you want to sell.
`city`             | City/District/Suburb/Town/Village.
`postalCode`       | ZIP or postal code.
`region`           | State/Region/Province.
`country`          | ISO 3166-1 alpha-2
`businessNumber`   | A valid business number. Quaderno validates EU VAT numbers, ABN, and NZBN.

### init( publishableKey, productId [, options] )

```js
  Quaderno.init(publishableKey, productId, {
    postalCode: "91104",
    country: "US"
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
`city`             | City/District/Suburb/Town/Village.
`postalCode`       | ZIP or postal code.
`region`           | State/Region/Province.
`country`          | ISO 3166-1 alpha-2.
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
  Quaderno.calculateTaxes().then(function(taxObject){
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

### destroy()
```js
  Quaderno.destroy();
```

Remove event listeners from the DOM elements. It might be useful if you have single-page apps and have tied the Quaderno object to a form via `initForm` or the [simple initialization](#quaderno-js-how-to-initialize-quaderno-js-simple-automatic-init).

### getProduct()
```js
  Quaderno.productId = "productId";

  Quaderno.getProduct().then(function(product){
    console.log(product.name);
  });
```

***Async request***. Retrieve the product info identified by `productId`. Returns a `product` object in the resolve function.

### readTaxes()
```js
  taxes = Quaderno.readTaxes();

  taxes.name;
  taxes.rate;
  taxe.country;
```

Reads the taxes cache and returns a `tax` object.

### reload()
```js
  Quaderno.productId = "anotherProduct";

  Quaderno.reload().then(function(response){
    console.log(response.product);
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

Please keep in mind that business numbers are not actually validated while using the Sandbox.

This method returns a `result` object with the following attribute:

- **result.valid**: true (valid business number), false (invalid business number) or null (the external validation service is temporarily unavailable).

### The Tax object

This object is passed by the resolve function of the `Promise` returned by the `Quaderno.calculateTaxes` method or by invoking `Quaderno.readTaxes`.

Attribute          | Description
-------------------|-----------------------------------------------------------------------------------------------------------
`country`          | A valid ISO 3166-1 alpha-2 code.
`region`           | State/Region/Province.
`county`           | County (only for US sales tax).
`name`             | Name of the tax.
`rate`             | Tax rate in percentage (6.5).
`extraName`        | Name of the extra tax.
`extraRate`        | Tax rate in percentage of the extra tax.
`taxClass`         | The tax class of the transaction. Can be one of the following: `standard`, `eservice`, `ebook` or `saas`.


### The Product object

This object contains the relevant information related to the productID you've set. It's returned by the resolve function of `Quaderno.init`, `Quaderno.initForm` (in conjunction with the gateway object) or `Quaderno.getProduct`.

Attribute          | Description
-------------------|-----------------------------------------------------------------------------------------------------------
`name`             | The name of the product.
`image`            | URL to the image of the product.
`unitCost`         | Unit price of the product.
`currency`         | A valid ISO-4271 code. 
`kind`             | Indicate if the product is either a `subscription` or a `one-off` sale.
`taxClass`         | The tax class for the product. Can be one of the following: `standard`, `eservice`, `ebook` or `saas`.
`taxType`          | Indicates if the tax is included or excluded from the product price.

## How to initialize Quaderno.js

### Simple automatic init

Automatically loads quaderno.js if the form has the `quaderno-payment-form` ID and a valid `data-publishable-key` and `data-product-id`.

- ***data-publishable-key***: Your Quaderno public key
- ***data-product-id***: The product code you've registered in Quaderno

The input fields will be automatically detected as long as they have the `data-quaderno` with their definition:

- ***city***
- ***region***
- ***postalCode***
- ***country*** (mandatory)
- ***businessNumber***

<div class="center-column"></div>
```html
    <head>
      <script src="https://js.quaderno.io/v3/"></script>
    </head>

    <body>
      <form id="quaderno-payment-form" data-publishable-key="pk_xxxxxxxx" data-product-id="prod_xxxxxxxx">
        <select data-quaderno="country"> … </select>
        <input data-quaderno="postalCode" />
        ...
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
      <form id="payment-form" data-publishable-key="pk_xxxxxxxx" data-product-id="prod_xxxxxxxx">
        <select data-quaderno="country" /> … </select>
        <input data-quaderno="postalCode" />
        <!-- … -->
      </form>

      <script>
        Quaderno.initForm('#payment-form').then(function(response) {
          console.log(response.product);
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
      Quaderno.init(publishableKey, productId,
        {
          country: $('#country').val(),
          postalCode: $('#postal-code').val(),
        }
      ).then(function(response) {
        console.log(response.product);
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
      console.log(response.product);
    }).catch(function(error) {
      console.log(error.description, error.messages);
    });
```

Usually you want to `reload` the checkout if the `productId` has changed, because taxes might change.

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
      Quaderno.calculateTaxes().then(function(taxObject){
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
