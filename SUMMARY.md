# Table of contents

## Bando for Developers <a href="#welcome" id="welcome"></a>

* [The Blockchain Spending Protocol](README.md)
* [Quickstart](welcome/overview-1.md)
* [Glossary](welcome/terminology.md)
* [Use Cases](welcome/use-cases.md)
* [Protocol Architecture](welcome/architecture/README.md)
  * [Payment Reference Validation](welcome/architecture/payment-reference-validation.md)
  * [Transaction Flow](welcome/architecture/order-request.md)
  * [Old Order request with escrow](welcome/architecture/order-request-1.md)
  * [Order Fulfillment](welcome/architecture/order-fulfillment.md)
  * [Refunds](welcome/architecture/refunds.md)
  * [Submitting a Fulfillment Result](welcome/architecture/refunds-1.md)

## Spending Widget <a href="#widget" id="widget"></a>

* [Widget Quickstart](widget/quick-start.md)
* [Configuration](widget/configure.md)
* [Customization](widget/customization.md)
* [Localization](widget/localization.md)
* [Wallet Management](widget/wallet-management.md)
* [Framework Integration](widget/framework-integration/README.md)
  * [Next.js](widget/framework-integration/nextjs.md)
  * [Svelte](widget/framework-integration/svelte.md)

## Bando Spending API

* [Get Started with the API](bando-spending-api/bando-api.md)
* [🧾 API Quickstart](bando-spending-api/complete-flow.md)
* [Authentication](bando-spending-api/authentication.md)
* [Guides](bando-spending-api/guides/README.md)
  * [Get Available Products](bando-spending-api/guides/get-available-products.md)
  * [Get Available Tokens for a Chain](bando-spending-api/guides/get-available-tokens-for-a-chain.md)
  * [Get a Payment Reference](bando-spending-api/guides/get-a-payment-reference.md)
  * [Validate a payment reference](bando-spending-api/guides/validate-a-payment-reference.md)
  * [Get a Quote](bando-spending-api/guides/get-a-quote.md)
* [API Reference](bando-spending-api/api-reference.md)

## EVM Smart Contracts

* [EVM Smart Contracts | Architecture](evm-smart-contracts/contract-architecture.md)
* [Contracts](evm-smart-contracts/contracts/README.md)
  * [Core](evm-smart-contracts/contracts/core/BandoERC20Fulfillable.md)
    * [BandoERC20Fulfillable](evm-smart-contracts/contracts/core/BandoERC20Fulfillable.md)
    * [BandoFulfillmentManager](evm-smart-contracts/contracts/core/BandoFulfillmentManager.md)
    * [BandoFulfillable](evm-smart-contracts/contracts/core/BandoFulfillable.md)
    * [BandoRouter](evm-smart-contracts/contracts/core/BandoRouter.md)
    * [FulfillmentTypes](evm-smart-contracts/contracts/core/FulfillmentTypes.md)
  * [Libraries](evm-smart-contracts/contracts/libraries/README.md)
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
