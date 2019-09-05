# Quaderno.js

## Introduction

This is the API reference for Quaderno.js. Use Quaderno.js’ APIs to calculate taxes in your forms in real time.

### Including Quaderno.js

However you’re using Quaderno.js, you always begin by including the library and setting your API key. To get started, include this script on your pages — it should always be loaded directly from https://js.quaderno.io:

<div class="center-column"></div>
```html
  <script src="https://js.quaderno.io/v4/"></script>
```

### Supported browsers

Quaderno.js strives to support all recent versions of major browsers. For the sake of security and providing the best experience to the majority of customers, we do not support browsers that are no longer receiving security updates and represent a small minority of traffic.

- We support Internet Explorer and Edge per **[Microsoft's lifecycle policy](https://support.microsoft.com/en-us/gp/microsoft-internet-explorer)**.
- We support Chrome and Safari on all platforms and Firefox on desktop platforms.
- We support the Android native browser on Android 4.4 and later.
- We respond to bug reports but do not proactively test other mobile browsers.

If you have an issue with Quaderno.js on a specific browser, please **[contact us](mailto:support@quaderno.io)** so we can improve its support.

## Reference


When you include the Quaderno.js script in your page, a global `Quaderno` object will be available with the following methods:

### init( publishableKey )

```js
  Quaderno.init(publishableKey).then(function(){
    console.log('Success');
  }).catch(function(error){
    console.log(error.description, error.messages);
  });
```

***Async request***. Use this method to initialize the global object `Quaderno`.


This method returns a `Promise` which resolves if the publishableKey verification was successful. If there's any issue, this method returns a `Promise` which rejects with an `error` object. This object has either:

- **error.description**: A full description of the error.
- **error.messages**: If present, it indicates the affected attribute and an specific error (ie. `{ email: "invalid", businessNumber: "invalid" }`).


### bindForm( formId )

```js
  Quaderno.bindForm("#formId").then(function(){
    console.log('Success');
  }).catch(function(error){
    console.log(error.description, error.messages);
  });
```

***Async request***. This method also initializes the global object `Quaderno` from a form object.

This method returns a `Promise` which resolves if the publishableKey verification was successful. If there's any issue, this method returns a `Promise` which rejects with an `error` object. This object has either:

- **error.description**: A full description of the error.
- **error.messages**: A brief description if each invalid attribute.

### calculateTaxes( options )

```js
  var options = { taxClass: 'eservice', postalCode: '123456', city: 'Dublin', country: 'IE' }

  Quaderno.calculateTaxes(options).then(function(taxObject){
    console.log(taxObject.name, taxObject.rate, taxObject.country); // ie. "VAT", 23, "IE"
  })
```

***Async request***. Calculates the taxes based on the billing data from `options` object

Available options are documented below:

Attribute          |  Mandatory | Description
-------------------|------------|-----------------------------------------------------------------------------------
`taxClass`         | No         | Indicates the tax class of the transaction.
`postalCode`       | No         | ZIP or postal code.
`city`             | No         | The customer's city. Used to make more accurate calculations based on the postal code
`country`          | No         | ISO 3166-1 alpha-2
`businessNumber`   | No         | A valid business number. Quaderno validates EU VAT numbers, ABN, and NZBN.

The `options` can be omitted if you've set a form using `bindForm` and the proper `data-quaderno`.

This method returns a `Promise` which resolves with a `tax` object.


### calculatePrice([options])

```js
  options = { amount: 100, quantiy: 2 }
  priceObject = Quaderno.calculatePrice(options);

  priceObject.subtotal;
  priceObject.taxes;
```

Calculates the final price of the product, tax inclusive.

Available options are documented below:

Attribute          |  Mandatory | Description
-------------------|------------|----------------------------------------------------------------------------------
`amount`           | No         | Amount you are going to charge your customer..
`quantity`         | No         | Quantity of the products.
`taxType`          | No         | If the tax is included` or `excluded`

The `options` can be omitted if you've set a form using `bindForm` and the proper `data-quaderno` or if you've retrieved the product information from Quaderno with `getProduct`


This method returns a `price` object with the following attributes:

- subtotal: the price without taxes and discounts.
- taxes: the taxes amount.
- total: the final amount.
- discountedAmount: The discount amount.

### destroy()
```js
  Quaderno.destroy();
```

Remove event listeners from the DOM elements. It might be useful if you have single-page apps and have tied the Quaderno object to a form via `bindForm` or the [simple initialization](#quaderno-js-how-to-initialize-quaderno-js-simple-automatic-init).

### getProduct( productId )
```js
  Quaderno.getProduct("productId").then(function(product){
    console.log(product.name, product.unitCost, product.code);
  });
```

***Async request***. Retrieve the product info identified by `productId`. Returns a `product` object in the resolve function.

### getCoupon( ['couponCode'] )
```js
  Quaderno.getCoupon("couponCode").then(function(product){
    console.log(coupon.amountOff);
  });
```

***Async request***. Retrieve the product info identified by `couponCode`. Returns a `coupon` object in the resolve function.

### getLastTax()
```js
  taxes = Quaderno.getLastTax();

  taxes.name;
  taxes.rate;
  taxe.country;
```

Reads the last calculated tax and returns a `tax` object.


### validateBusinessNumber([businessNumber, country])
```js
  Quaderno.validateBusinessNumber("GB1234567", "GB").then(function(response){
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

This object is passed by the resolve function of the `Promise` returned by the `Quaderno.calculateTaxes` method or by invoking `Quaderno.getLastTax`.

Attribute             | Description
----------------------|-----------------------------------------------------------------------------------------------------------
`country`             | A valid ISO 3166-1 alpha-2 code.
`region`              | State/Region/Province.
`county`              | County (only for US sales tax).
`city`                | City (only for US sales tax)
`countyTaxCode`       | County tax code (if applicable on US sales taxes)
`cityTaxCode`         | City tax code (if applicable on US sales taxes)
`name`                | Name of the tax.
`rate`                | Tax rate in percentage (6.5).
`extraName`           | Name of the extra tax.
`extraRate`           | Tax rate in percentage of the extra tax.
`taxClass`            | The tax class of the transaction. Can be one of the following: `standard`, `eservice`, `ebook` or `saas`.
`businessNumberValid` | This attribute is only present if you've set a `businessNumber` during the calculation request. It can be `true`, `null` or `false`.

### The Product object

This object contains the relevant information related to the productID you've set. It's returned by the resolve function of `Quaderno.getProduct`.

Attribute          | Description
-------------------|-----------------------------------------------------------------------------------------------------------
`code`             | The product code in Quaderno.
`name`             | The name of the product.
`image`            | URL to the image of the product.
`unitCost`         | Unit price of the product.
`currency`         | A valid ISO-4271 code.
`kind`             | Indicates if the product is either a `subscription` or a `one-off` sale.
`taxClass`         | The tax class for the product. Can be one of the following: `standard`, `eservice`, `ebook` or `saas`.
`taxType`          | Indicates if the tax is included or excluded from the product price.

### The Coupon object

This object contains the relevant information related to the couponCode you've set. It's returned by the resolve function of `Quaderno.getCoupon`.

Attribute          | Description
-------------------|-----------------------------------------------------------------------------------------------------------
`code`             | The product code in Quaderno.
`kind`             | Indicates if the coupon is `percent_off` or `amount_off`
`amountOff`        | The amount off.
`currency`         | The currency of the amount off.
`percentOff`       | The percent off.
`redeemable`       | Indicates if the coupon is redeemable


## How to initialize Quaderno.js

### Simple automatic init

Automatically loads quaderno.js if the form has the `quaderno-payment-form` ID and a valid `data-publishable-key`. Optionally you can also set a `data-product-id`.

- ***data-publishable-key***: Your Quaderno public key

The input fields will be automatically detected as long as they have the `data-quaderno` with their definition:

- ***postalCode*** (highly recommendable)
- ***country*** (mandatory)
- ***businessNumber***
- ***city***
- ***productId***
- ***amount***
- ***quantity***
- ***taxType***
- ***taxClass***
- ***productId***
- ***couponCode***

<div class="center-column"></div>
```html
    <head>
      <script src="https://js.quaderno.io/v4/"></script>
    </head>

    <body>
      <form id="quaderno-payment-form" data-publishable-key="pk_xxxxxxxx">
        <select data-quaderno="country"> … </select>
        <input data-quaderno="postalCode" />
        ...
      </form>
    </body>
```

### Deferred automatic init

If you don't want the form to load automatically, you can load it later by not using the ***"quaderno-payment-form"*** id, and calling the `bindForm` method whenever you want.

<div class="center-column"></div>

```html
    <head>
      <script src="https://js.quaderno.io/v4/"></script>
    </head>

    <body>
      <form id="payment-form" data-publishable-key="pk_xxxxxxxx" data-product-id="prod_xxxxxxxx">
        <select data-quaderno="country" /> … </select>
        <input data-quaderno="postalCode" />
        <!-- … -->
      </form>

      <script>
        Quaderno.bindForm('#payment-form').then(function(response) {
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
      Quaderno.init(publishableKey).then(function() {
        console.log('Success');
      }).catch(function(error) {
        console.log(error.description, error.messages);
      });
    </script>
```


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
    function reloadTaxes(options){
      Quaderno.calculateTaxes(options).then(function(taxObject){
        document.querySelector('tax-rate').innerHTML = taxObject.name + " " + taxObject.rate + "%"; // ie.: VAT 20%
      })
    }

    // Handle the change events on the relevant user-inputs
    document.querySelector('#country').addEventListener('change', function () {
      country = this.value
      postalCode = document.querySelector('#postal-code').value
      businessNumber = document.querySelector('#business-number').value

      reloadTaxes({ country: country, postalCode: postalCode, businessNumber: businessNumber})
    })

    document.querySelector('#postal-code').addEventListener('change', function () {
      postalCode = this.value
      country = document.querySelector('#country').value
      businessNumber = document.querySelector('#business-number').value

      reloadTaxes({ country: country, postalCode: postalCode, businessNumber: businessNumber })
    })

    document.querySelector('#business-number').addEventListener('change', function () {
      businessNumber = this.value
      country = document.querySelector('#country').value
      postalCode = document.querySelector('#postal-code').value

      Quaderno.validateBusinessNumber(businessNumber, country).then(function(){
        reloadTaxes({ businessNumber: businessNumber, country: country, postalCode: postalCode })
      })
    })
  </script>
```

You can use Quaderno.js to calculate taxes by invoking the method `Quaderno.calculateTaxes`.

If you are showing real time taxes calculations it's usually a good idea to force taxes calculations when the country, postal code or business number have changed.