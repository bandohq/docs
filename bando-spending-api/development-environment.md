---
description: Simulate transactions and failures. Use arbitrum as the test network.
---

# Development Environment

### URL

To test your integration we have a simple development environment. The API is hosted on:

```
https://apidev.bando.cool
```

### Dev Product Catalog

The catalog is not exactly the same but contains almost all brands and products. With only a few exceptions. The logo images and categories may differ.

### Test Blockchains

Currently we support Arbitrum to test purchases to enable very cheap fees and Worldchain to support world miniapps.

{% hint style="warning" %}
We do not support testnets due to the lack of liquidity and other limitations.
{% endhint %}

### Simulate Succesful Transactions

Every product except the `Sandbox` brand will be completed successfully.

### Simulate Failures

To simulate a failure, follow these steps:

* Choose the Sandbox brand product from the US catalog
* Get a quote.
* Submit a transaction
* Handle error response
* Verify refund was made
