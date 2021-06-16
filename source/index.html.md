---
title: Quaderno API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href="https://developers.quaderno.io/api" target="_blank">API reference</a>
  - <a href="https://quaderno.io" target="_blank">Quaderno Homepage</a>
  - <a href="https://support.quaderno.io" target="_blank">Knowledgebase and Support</a>
  - <a href="https://github.com/quaderno/quaderno-developers" target="_blank">Contributing to Docs</a>
  - <a href="https://github.com/tripit/slate" target="_blank">Documentation Powered by Slate</a><br /><br />

includes:
  - quaderno_js
  - checkout
  - apps

search: true

code_clipboard: true
---

# Introduction

Quaderno is a SaaS app and set of APIs to automate and improve tax management in businesses of all sizes. Here are some resources that will help you understand the basics of all of them.

The documentation is organized into these major areas:

- **[Quaderno Sandbox](#quaderno-sandbox)** is a safe space to experiment, test new features, and break things while learning about our Quaderno products.
- **[Quaderno.js](#quaderno-js)** is a handy small javascript library to calculate sales taxes in your own checkout form, directly from your frontend.
- **[Quaderno Checkout](#quaderno-checkout)** is a checkout form that you can use within your site to sell your own products and subscriptions.
- **[Quaderno Apps](#apps)** allows you to create new partner integrations based on OAuth2 and thus removing the need of using private keys.
- **[Quaderno API ↗](https://developers.quaderno.io/api)** are a set of APIs that provides your business with billing, reporting and tax management capabilities. With our APIs you can [calculate taxes during checkout](https://developers.quaderno.io/api/#calculating-a-tax-rate), [validate tax IDs](https://developers.quaderno.io/api/#validate-a-tax-id) or [manage location evidences](https://developers.quaderno.io/api/#evidence). You can [issue and manage invoices](https://developers.quaderno.io/api/#create-an-invoice) and credit notes or log your expenses, as well as asynchronously get all your [tax and business reports](https://developers.quaderno.io/api/#reporting) programmatically.
- **[Quaderno Connect API ↗](https://support.quaderno.io/quaderno-connect)** is our API to provide marketplaces with tax capabilities to their business models.

**Not a developer?**

A custom Quaderno integration requires you to either have basic coding skills or hire a developer. But we made Quaderno really easy to connect with many third-party platforms without coding experience at all. Check our documentation on [_one-click_ integrations](https://support.quaderno.io/platforms-and-integrations), such as for Shopify, Amazon FBA, and WooCommerce.

**Need more help?**

Just [send us an email](mailto:support+dev@quaderno.io). Please include your code or the API request and response when possible. We’re always happy to help out with code or any other questions you might have.

# Quaderno Sandbox

The Quaderno Sandbox mirrors the features found on the Quaderno Production servers and lets  you test our API and/or connect your trial or test mode accounts from other services like Stripe, PayPal, Braintree, etc.

The Sandbox has parity with the Quaderno main feature set supported by the live environment. This means you can test your Quaderno processes and know they will behave the same on the production servers as they do in the Sandbox environment.

**Your credentials for the Production environment does not work on the Sandbox.** You need to [create a new account](https://sandbox-quadernoapp.com/signup).

By using your Sandbox account, you can test and debug your application without referencing any real Quaderno users or their live Quaderno accounts. The Sandbox lets you operate your application in a safe environment and provides you a way to fine tune your Quaderno routines before moving your product into production.

We’re always happy to help out with code or any other questions you might have. Please [drop us a line](mailto:support@quaderno.io).

<aside class="warning">
Please keep in mind that while you can test all the API methods and plugins against the sandbox it has some limitations, most notably:
<ul>
<li>Emails are delivered to the account email rather than to the specified recipient.</li>
<li>There's a global 200 documents cap. If you need to create more you'll have to remove some of your previous documents. You can remove all your documents by navigating to <strong>You > Purge</strong> your data at any time.
</li>
<li>Tax IDs (VAT numbers, ABN, etc.) are not validated in the Sandbox (**)</li>
<li>Only state tax rates are returned by the Taxes API. Local tax rates will not be returned, with the exception of 90049, 10128, 60611, and 33132 for testing purposes.</li>
<li>Tax thresholds are not updated in the Sandbox.</li>
</ul>
</aside>

** Generally, the sandbox will return all VAT numbers as valid. However, we recently added the following test numbers so you can test different scenarios in your Sandbox account:

- **DE111111111**: invalid number, returns `false`
- **IE222222222**: node down, returns `null`
