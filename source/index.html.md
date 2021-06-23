---
title: Quaderno API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href="https://developers.quaderno.io/api" target="_blank">API reference</a>
  - <a href="https://quaderno.io" target="_blank">Quaderno Homepage</a>
  - <a href="https://support.quaderno.io" target="_blank">Quaderno Support Center</a>
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

**Quaderno Sandbox is a safe space to experiment** with our set of APIs and products, testing new features or fine-tunning your Quaderno workflows and integrations, without the worries of breaking things while learning.

**Quaderno Sandbox has feature parity with the real live environment plus all the new features**, testable in advance but without the fuss of dealing with real user data! It's perfect for connecting your payment's processor testing accounts, knowing all will behave just as fine on the live production servers.

**Quaderno Sandbox is a separate environment, with it's own URL `https://sandbox-quadernoapp.com` and credentials**. This means *your credentials for the Production environment won't work on Quaderno Sandbox.* You'll need to [create a new Quaderno Sandbox account](https://sandbox-quadernoapp.com/signup).

<aside class="info">
<strong>⚠️ Quaderno Sandbox has some limitations to provide an improved testing experience:</strong>
</aside>
- Emails are delivered to the account email rather than to the specified recipient.
- Tax IDs always return `{ "valid": true }` except for:
  - **DE111111111**: acts as an invalid Tax ID, returning `{ "valid": false }`
  - **IE222222222**: acts as if the Tax Validator service is down, returning `{ "valid": null }`

> Purge your Quaderno Sandbox documents with:

```shell
curl --user sk_your-secret-key:x \
     --request POST \
     --url http://your-url.sandbox-quadernoapp.com/api/purge
```
- The Taxes API only returns state tax rates. Local tax rates will not be returned, with the exception of `90049`, `10128`, `60611`, and `33132`.
- Tax thresholds are not updated.

- There's a global 200 documents cap. If you need to create more you'll have to remove your previous documents first, by navigating to <strong>You > Purge</strong> into the app, or using the `/purge` endpoint as shown on the right panel.
