# Table of contents

## Bando for Developers <a href="#welcome" id="welcome"></a>

* [The On-chain Spending Protocol](README.md)
* [Quickstart](welcome/overview-1.md)
* [Glossary](welcome/terminology.md)
* [Use Cases](welcome/use-cases.md)
* [Protocol Architecture](welcome/architecture/README.md)
  * [Payment Reference Validation](welcome/architecture/payment-reference-validation.md)
  * [Order Request](welcome/architecture/order-request.md)
  * [Order Fulfillment](welcome/architecture/order-fulfillment.md)
  * [Refunds](welcome/architecture/refunds.md)

## Spending Widget <a href="#widget" id="widget"></a>

* [Widget Quick Start](widget/quick-start.md)
* [Installation](widget/installation.md)
* [Configuration](widget/configure.md)
* [Customization](widget/customization.md)
* [Localization](widget/localization.md)
* [Wallet Management](widget/wallet-management.md)
* [Framework Integration](widget/framework-integration/README.md)
  * [Next.js](widget/framework-integration/nextjs.md)
  * [Svelte](widget/framework-integration/svelte.md)

## Fulfiller API

* [Get Started with the API](fulfiller-api/bando-api.md)
* [Authentication](fulfiller-api/authentication.md)
* [Guides](fulfiller-api/guides/README.md)
  * [Get Available Products](fulfiller-api/guides/get-available-products.md)
  * [Get a Payment Reference](fulfiller-api/guides/get-a-payment-reference.md)
  * [Validate a payment reference](fulfiller-api/guides/validate-a-payment-reference.md)
  * [Get a Quote](fulfiller-api/guides/get-a-quote.md)
  * [Get Available Tokens for a Chain](fulfiller-api/guides/get-available-tokens-for-a-chain.md)
* [API Reference](fulfiller-api/api-reference/README.md)
  * [Product Catalog API](fulfiller-api/api-reference/catalog.md)
  * [Payment Reference API](fulfiller-api/api-reference/ref.md)
  * [Quote API](fulfiller-api/api-reference/quote.md)
  * [Assets API](fulfiller-api/api-reference/assets-api.md)

## EVM Smart Contracts

* [EVM Smart Contracts | Architecture](evm-smart-contracts/contract-architecture.md)
* [Contracts](evm-smart-contracts/contracts/core-concepts.md)
  * [Core](evm-smart-contracts/contracts/core/BandoERC20Fulfillable.md)
    * [BandoERC20Fulfillable](evm-smart-contracts/contracts/core/BandoERC20Fulfillable.md)
    * [BandoFulfillmentManager](evm-smart-contracts/contracts/core/BandoFulfillmentManager.md)
    * [BandoFulfillable](evm-smart-contracts/contracts/core/BandoFulfillable.md)
    * [BandoRouter](evm-smart-contracts/contracts/core/BandoRouter.md)
    * [FulfillmentTypes](evm-smart-contracts/contracts/core/FulfillmentTypes.md)
  * [Libraries](evm-smart-contracts/libraries/README.md)
    * [FulfillmentRequestLib](evm-smart-contracts/contracts/libraries/FulfillmentRequestLib.md)
    * [SwapLib](evm-smart-contracts/contracts/libraries/SwapLib.md)
  * [Periphery](evm-smart-contracts/contracts/periphery/README.md)
    * [ERC20TokenRegistry](evm-smart-contracts/contracts/periphery/ERC20TokenRegistry.md)
    * [FulfillableRegistry](evm-smart-contracts/contracts/periphery/FulfillableRegistry.md)
  * [Proxy](evm-smart-contracts/contracts/proxy/ProxyOverview.md)
    * [Upgradeability](evm-smart-contracts/contracts/proxy/upgradeability.md)
* [Security](evm-smart-contracts/security/README.md)
  * [Access Control](evm-smart-contracts/security/access-control.md)
  * [Security Considerations](evm-smart-contracts/security/security-considerations.md)
  * [Rekt Test](evm-smart-contracts/security/rekt-test.md)
  * [Audits](evm-smart-contracts/security/audits.md)
* [Code](https://github.com/bandohq/evm-fulfillment-protocol)
