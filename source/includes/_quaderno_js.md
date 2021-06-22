# Quaderno.js

Quaderno.js is a handy small javascript library to calculate taxes directly from your frontend. It fits great with our own checkout experience, [Quaderno Checkout](https://www.quaderno.io/checkout), as well as with your own forms, to calculate taxes in real time.

To unleash all the power of our server-side APIs like Tax calculations and Tax ID validations, Billing and Tax reporting and tracking, go check our [API](https://developers.quaderno.io/api).

## Including Quaderno.js

```html
  <script src="https://js.quaderno.io/v4/"></script>
```

However you’re using Quaderno.js, you always begin by including the library and setting your __public API key*__.

To get started, include this script on your pages — it should always be loaded directly from `https://js.quaderno.io`

<aside class="warning">
<strong>*</strong> Please note that we provide a differentiated publishable API key for Quaderno.js for security reasons. This public API key is different from your account's private API key, which should never be exposed on your frontend.
<br><br>
You can tell the difference by their naming:
<ul>
<li>private key starts with <code>sk_</code>, which stands for "secret key"</li>
<li>public key starts with <code>pk_</code>, which stands for "public key"</li>
</ul>
</aside>

While the private API key is meant for being used server-side, secured in your backend, the public API key is only used by Quaderno.js, and only allow access to our tax calculations engine. No data can be leaked from your account with the public API key.

## Supported browsers

Quaderno.js strives to support all recent versions of major browsers. For the sake of security and providing the best experience to the majority of customers, we do not support browsers that are no longer receiving security updates and represent a small minority of traffic.

- We support Internet Explorer and Edge per **[Microsoft's lifecycle policy](https://support.microsoft.com/en-us/gp/microsoft-internet-explorer)**.
- We support Chrome and Safari on all platforms and Firefox on desktop platforms.
- We support the Android native browser on Android 4.4 and later.
- We respond to bug reports but do not proactively test other mobile browsers.

If you have an issue with Quaderno.js on a specific browser, please **[contact us](mailto:support@quaderno.io)** so we can improve its support.

## Quaderno.js methods

When you include the Quaderno.js script in your page, a global `Quaderno` object will be available with the following methods:

### init( publishableKey )

```js
  // never expose your private API key on your frontend, use the public API key instead
  Quaderno.init(publishableKey).then(function(){
    console.log('Success');
  }).catch(function(error){
    console.log(error.description, error.messages);
  });
```

***Async request***. Use this method to initialize the global object `Quaderno`.

<aside class="info">
Use <code>init</code> for initializing Quaderno.js unbinded from the UI, as explained in <a href="#initializing-quaderno-js">initializing Quaderno.js</a> – Custom Init. This means you won't be using custom attributes like <code>id="quaderno-payment-form" data-publishable-key="pk_xxxxxxxx"</code> in your forms, but just pure javascript.
</aside>

This method returns a `Promise` which resolves if the publishableKey verification was successful. If there's any issue, this method returns a `Promise` which rejects with an `error` object. This object has either:

- **error.description**: A full description of the error.
- **error.messages**: If present, it indicates the affected attribute and an specific error (ie. `{ email: "invalid", businessNumber: "invalid" }`).


### bindForm( formId )

```js
  // to use along with the deferred automatic init
  Quaderno.bindForm("#formId").then(function(){
    console.log('Success');
  }).catch(function(error){
    console.log(error.description, error.messages);
  });
```

***Async request***. This method also initializes the global object `Quaderno` from a form object.

<aside class="info">
Use this method when you need to use your own <code>id</code> in your forms or when you want to not load the form with the page but later, in conjuction with the <a href="#deferred-automatic-init">deferred automatic init</a>.

If you want to completely detach from the UI and just use pure javascript, see the Custom Init in <a href="#initializing-quaderno-js">initializing Quaderno.js</a>.
</aside>

This method returns a `Promise` which resolves if the publishableKey verification was successful. If there's any issue, this method returns a `Promise` which rejects with an `error` object. This object has either:

- **error.description**: A full description of the error.
- **error.messages**: A brief description if each invalid attribute.

### calculateTaxes( options )

```js
  // use `options` along with Custom init
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

```html
  <!-- no need to fill the `options` of calculateTaxes() with automatic inits -->
  <form id="quaderno-payment-form" data-publishable-key="pk_xxxxxxxx">
    <select data-quaderno="country"> … </select>
    <input data-quaderno="postalCode" />
    ...
  </form>
```

<aside class="info">
The <code>options</code> can be omitted if you've set a form using <code>bindForm</code> and the proper <code>data-quaderno</code>, or used the <a href="#simple-automatic-init">simple automatic init</a>.
</aside>

This method returns a `Promise` which resolves with a [tax object](#the-tax-object).

Note that when you calculate taxes using our [Tax Rates API](https://developers.quaderno.io/api/#calculate-a-tax-rate), parameters follow a slighty different naming scheme:

Quaderno.js      | Tax Rates API
-----------------|--------------
`taxClass`       | `tax_code`
`businessNumber` | `tax_id`
`taxType`       | `tax_behavior`

### calculatePrice([options])

```js
  options = { amount: 100, quantity: 2 }
  priceObject = Quaderno.calculatePrice(options);

  priceObject.subtotal;
  priceObject.taxes;
```

Helper method which calculates the final price of the product, tax inclusive.

Available options are documented below:

Attribute          |  Mandatory | Description
-------------------|------------|----------------------------------------------------------------------------------
`amount`           | No         | Amount you are going to charge your customer..
`quantity`         | No         | Quantity of the products.
`taxType`          | No         | If the tax is `included` or `excluded`

The `options` can be omitted if you've set a form using `bindForm` with its proper `data-quaderno` attributes, or if you've retrieved the product information from Quaderno with `getProduct`


This method returns a `price` object with the following attributes:

- `subtotal`: the price without taxes and discounts.
- `taxes`: the taxes amount.
- `total`: the final amount.
- `discountedAmount`: The discount amount.

### destroy()
```js
  Quaderno.destroy();
```

Remove event listeners from the DOM elements. It might be useful if you have single-page apps and have tied the Quaderno object to a form via `bindForm` or the [simple initialization](#simple-automatic-init).

### getProduct( productId )
```js
  Quaderno.getProduct("productId").then(function(product){
    console.log(product.name, product.unitCost, product.code);
  });
```

***Async request***. Retrieve the product info identified by `productId`. Returns a [`product` object](#the-product-object) in the resolve function.

You can register your products manually in [quadernoapp.com/products](https://quadernoapp.com/products) or [programatically with our API](https://developers.quaderno.io/api/#products).

### getCoupon( couponCode )
```js
  Quaderno.getCoupon("couponCode").then(function(product){
    console.log(coupon.amountOff);
  });
```

***Async request***. Retrieve the product info identified by `couponCode`. Returns a `coupon` object in the resolve function.

You can register your products manually in [quadernoapp.com/checkout/coupons](https://quadernoapp.com/checkout/coupons) or [programatically with our API](https://developers.quaderno.io/api/#coupons).

### getLastTax()
```js
  taxes = Quaderno.getLastTax();

  taxes.name;
  taxes.rate;
  taxes.country;
```

Reads the last calculated tax and returns a [`tax` object](#the-tax-object).

<aside class="notice">
This performant method allows you to avoid repeatedly calling our API during your checkout process when you know the parameters won't change. Please use it whenever you can!
</aside>

### validateBusinessNumber(businessNumber, country)
```js
  Quaderno.validateBusinessNumber("GB1234567", "GB").then(function(response){
    console.log(response.valid);
  }).catch(function(error){
    console.log(error.descriptions);
  });

```

***Async request***. This method validates the customer's business number. Currently Quaderno validates EU VAT numbers, ABN, and NZBN.

Please keep in mind that business numbers are not actually validated when using [Quaderno Sandbox](#quaderno-sandbox).

This method returns a `result` object with the following attribute:

- **result.valid**: true (valid business number), false (invalid business number) or null (the external validation service is temporarily unavailable).

## Quaderno.js objects

This section documents the objects you'll receive from Quaderno.js.
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


## Initializing Quaderno.js

For your convenience, there are a few different ways to initialize Quaderno.js.

<aside class="info">
With the automatic inits (simple and deferred) Quaderno.js attaches itself to the DOM, looking for <code>data-quaderno</code> attributes in your <code>form</code>s to automatically fill the <code>options</code> for <a href="#calculatetaxes-options">calculateTaxes()</a>.
</aside>

You can avoid this behavior using the [custom init](#custom-init) method.

### Simple automatic init

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

Automatically loads quaderno.js if the form has the `quaderno-payment-form` id and a valid `data-publishable-key`. Optionally you can also set a `data-product-id`.

- ***data-publishable-key***: Your Quaderno _public_ key. Remember to never expose your _private_ API keys.

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


### Deferred automatic init

```html
    <head>
      <script src="https://js.quaderno.io/v4/"></script>
    </head>

    <body>
      <form id="payment-form" data-publishable-key="pk_xxxxxxxx" data-product-id="prod_xxxxxxxx">
        <select data-quaderno="country"> … </select>
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

If you don't want the form to load automatically, you can load it later by not using the ***"quaderno-payment-form"*** id, and then call the `bindForm` method whenever you want.

### Custom init

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
        console.log('Quaderno.js successfully initialized');
      }).catch(function(error) {
        console.log(error.description, error.messages);
      });
    </script>
```

Futhermore, you can also dissociate Quaderno from the DOM by setting the Quaderno object properties by yourself.

This means you need to explicitly set your `publishableKey` as argument for the `init` call, instead of using the `data-publishable-key` html attribute in your `form`. 

Afterwards, you'll need to specify all `options` for [`calculateTaxes()`](#calculatetaxes-options).

## Calculating taxes from your frontend with Quaderno.js

> In this example, we are going to handle UI updates on the relevant fields to force tax calculations and show it in a `<span>`. We'll be validating Tax IDs (business numbers) too when given one. We'll use the custom init method in this example.

```html
<form>
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
    </select>
  </label>

  <label for="postal-code">
    Postal code
    <input id="postal-code">
  </label>

  <label for="business-number">
    Business number:
    <input id="business-number">
  </label>

    <label for="tax-class">
    Tax Code:
    <select id="tax-class">
      <option value="standard">standard</option>
      <option value="eservice">eservice</option>
      <option value="ebook">ebook</option>
      <option value="saas">saas</option>
    </select>
  </label>

</form>
  Taxes: <span id="tax-rate"></span>

  <script>

    // never expose your private API key on your frontend, use the public API key instead
    let publishableKey = "pk_test_xxxxxxxxxxxxxxxxxxxx";
    // we're not attaching Quaderno.js to our form, so we need to use init:
    Quaderno.init(publishableKey).then(function(){
      console.log('Quaderno.js successfully initialized');
    }).catch(function(error){
      console.log(error.description, error.messages);
    });

    // this is a custom function to be called each time the user introduces new data
    function reloadTaxes(options){
      console.log("Calling calculateTaxes with params", options)
      Quaderno.calculateTaxes(options).then(function(taxObject){
        // Remember, you need to be registered on the tax jurisdiction to get the tax rate
        console.log("Taxes calculated:", taxObject)
        document.querySelector('#tax-rate').innerHTML = taxObject.name + " " + taxObject.rate + "%"; // ie.: VAT 20%
      })
    }

    // Handle change events on the relevant user-inputs so that the Tax Rate it's automatically updated
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

      // We're also validating Tax ID (business number) when given one
      Quaderno.validateBusinessNumber(businessNumber, country).then(function(){
        reloadTaxes({ businessNumber: businessNumber, country: country, postalCode: postalCode })
      })
    })

    document.querySelector('#tax-class').addEventListener('change', function () {
      taxClass = this.value
      country = document.querySelector('#country').value
      postalCode = document.querySelector('#postal-code').value
      businessNumber = document.querySelector('#business-number').value

      reloadTaxes({ taxClass: taxClass, country: country, postalCode: postalCode, businessNumber: businessNumber })
    })
  </script>
```

The more common use of Quaderno.js is to calculate taxes by invoking the method `Quaderno.calculateTaxes`.

In the example on the right, we're showing a form with different countries and tax codes (called `taxClass` in Quaderno.js). Instead of using the super-simple approach of binding to your form by specifying a `quaderno-payment-form` id and a valid `data-publishable-key`, we're first initializing Quaderno.js with our public API key using the [custom init](#custom-init) method, and then collecting the data that will form our `options` for `calculateTaxes(options)` manually, with `document.querySelector('#value-id')`, anytime these receive a `change` event from the DOM.

The result from `calculateTaxes(options)` is then painted on the UI. We're also validating the Tax ID (business number) with `validateBusinessNumber` when given one.

> On this example, when selecting "United States" and introducing "7501" as postal code, the `console` prints:

```
Calling calculateTaxes with params:
{
    "country": "US",
    "postalCode": "7501",
    "businessNumber": ""
}

Taxes calculated:
{
  country: "US"
  extraName: null
  extraRate: null
  name: "Sales tax"
  rate: 6.25
  region: "TX"
}
```

Note that we're not calling the performant `getLastTax()` method because in this case the data changes everytime. If you already got your tax rate and need that data again later in your checkout process, you may want to call `calculateTaxes(options)` once and after that rely on `getLastTax()`.

Remember that Quaderno.js will only return tax rates for those tax jurisdictions where you are registered. Follow the links to learn more on [tax jurisdictions](https://support.quaderno.io/tax-jurisdictions) and [setting up tax jurisdictions](https://support.quaderno.io/setting-up-tax-jurisdictions).