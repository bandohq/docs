---
description: Integrate with our API in minutes.
---

# Getting started

> This guide walks technical users (integrators) through the process of spending via the **Bando Spending Protocol (BSP)**. The flow involves retrieving products, creating a quote, paying with a wallet, registering the transaction, and tracking its status.

📘 [OpenAPI Specification](https://api.bando.cool/api/v1/openapi.json)

***

## Full Flow Summary

<table data-full-width="true"><thead><tr><th>Step</th><th>Action</th><th>Method</th><th width="248.4609375">Endpoint</th></tr></thead><tbody><tr><td>1</td><td><a href="getting-started.md#id-1.-discover-available-products">Get products</a></td><td><code>GET</code></td><td><code>/products/grouped/</code></td></tr><tr><td>2</td><td><a href="getting-started.md#id-2.-fetch-available-chains-and-supported-assets">Get supported assets</a></td><td><code>GET</code></td><td><code>/tokens/{networkKey}/</code></td></tr><tr><td>3</td><td><a href="getting-started.md#id-3.-create-a-quote">Create quote</a></td><td><code>POST</code></td><td><code>/quotes/</code></td></tr><tr><td>4</td><td><a href="getting-started.md#id-4.-perform-wallet-payment">Wallet payment (external)</a></td><td>Performed in the client</td><td>N/A</td></tr><tr><td>5</td><td><a href="getting-started.md#id-5.-register-spending-transaction">Register transaction</a></td><td><code>POST</code></td><td><code>/wallets/{address}/transactions/</code></td></tr><tr><td>6</td><td><a href="getting-started.md#id-6.-track-transaction-status">Track transaction status</a></td><td><code>GET</code></td><td><code>/wallets/{address}/transactions/{transactionId}/</code></td></tr></tbody></table>

## 1. 🔎 Discover Available Products

**Endpoint:** `GET /products/grouped/`

This endpoint returns all spendable products grouped by category.

{% openapi-operation spec="bando-api" path="/products/grouped/" method="get" %}
[OpenAPI bando-api](https://api.bando.cool/api/v1/openapi.json)
{% endopenapi-operation %}

***

## 2. Fetch available chains and supported assets

**Endpoints:**

* `GET /networks/`
* &#x20;`GET /tokens/{networkKey}`&#x20;

{% openapi-operation spec="bando-api" path="/networks/" method="get" %}
[OpenAPI bando-api](https://api.bando.cool/api/v1/openapi.json)
{% endopenapi-operation %}

{% openapi-operation spec="bando-api" path="/tokens/{networkKey}/" method="get" %}
[OpenAPI bando-api](https://api.bando.cool/api/v1/openapi.json)
{% endopenapi-operation %}

***

## 3. 💬 Create a Quote

**Endpoint:** `POST /quotes/`

{% openapi-operation spec="bando-api" path="/quotes/" method="post" %}
[OpenAPI bando-api](https://api.bando.cool/api/v1/openapi.json)
{% endopenapi-operation %}

***

## 4. 💸 Perform Wallet Payment

When you're outside the BSP, you can use the quote response `transactionRequest` property to send crypto using a wallet (such as MetaMask, WalletConnect, CLI, etc.).&#x20;

Here's what you do:&#x20;

* Wait for the transaction to get a receipt. We recommend to wait for at last one confirmation on the blockchain.
* Start the transfer and make sure to grab the **payment transaction hash**.
* &#x20;Th transaction returned by the API is compatible with most JS clients. But make sure to format it according to your implementation.

***

## 5. 📝 Register Spending Transaction

**Endpoint:** `POST /wallets/{address}/transactions/`

Use the sender’s wallet address in the path. Submit both `quoteId` and the `paymentTxHash` from the previous step.

{% openapi-operation spec="bando-api" path="/wallets/{address}/transactions/" method="post" %}
[OpenAPI bando-api](https://api.bando.cool/api/v1/openapi.json)
{% endopenapi-operation %}

***

## 6. 🔍 Track Transaction Status

**Endpoint:** `GET /wallets/{address}/transactions/{transactionId}/`

Use this to check the current status (`PENDING`, `SUCCESS`, or `FAILED`) of the transaction you registered.

{% openapi-operation spec="bando-api" path="/wallets/{address}/transactions/{transactionId}/" method="get" %}
[OpenAPI bando-api](https://api.bando.cool/api/v1/openapi.json)
{% endopenapi-operation %}

***
