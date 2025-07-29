---
description: >-
  This guide walks technical users (integrators) through the process of spending
  via the **Bando Spending Protocol (BSP)**.
---

# 🧾 API Quickstart

> This guide walks technical users (integrators) through the process of spending via the **Bando Spending Protocol (BSP)**. The flow involves retrieving products, creating a quote, paying with a wallet, registering the transaction, and tracking its status.

📘 [OpenAPI Specification](https://api.bando.cool/api/v1/openapi.json)

***

## General diagram

![alt text](../fulfiller-api/guides/complete-flow.svg)

## 1. 🔎 Discover Available Products

**Endpoint:** `GET /products/grouped/`

This endpoint returns all spendable products grouped by category.

{% openapi-operation spec="bando-api" path="/products/grouped/" method="get" %}
[OpenAPI bando-api](https://api.bando.cool/api/v1/openapi.json)
{% endopenapi-operation %}

***

## 2. 💬 Create a Quote

**Endpoint:** `POST /quotes/`

{% openapi-operation spec="bando-api" path="/quotes/" method="post" %}
[OpenAPI bando-api](https://api.bando.cool/api/v1/openapi.json)
{% endopenapi-operation %}

***

## 3. 💸 Perform Wallet Payment

This step occurs **outside BSP** and involves using the quote response (e.g. `walletAddress`, `amount`) to send crypto via wallet (MetaMask, WalletConnect, CLI, etc.).

You must:

* Initiate the transfer
* Capture the **payment transaction hash**

💡 Use base units (e.g. wei) and ensure the amount and destination address match the quote.

***

## 4. 📝 Register Spending Transaction

**Endpoint:** `POST /wallets/{address}/transactions/`

Use the sender’s wallet address in the path. Submit both `quoteId` and the `paymentTxHash` from the previous step.

{% openapi-operation spec="bando-api" path="/wallets/{address}/transactions/" method="post" %}
[OpenAPI bando-api](https://api.bando.cool/api/v1/openapi.json)
{% endopenapi-operation %}

***

## 5. 🔍 Track Transaction Status

**Endpoint:** `GET /wallets/{address}/transactions/{transactionId}/`

Use this to check the current status (`PENDING`, `SUCCESS`, or `FAILED`) of the transaction you registered.

{% openapi-operation spec="bando-api" path="/wallets/{address}/transactions/{transactionId}/" method="get" %}
[OpenAPI bando-api](https://api.bando.cool/api/v1/openapi.json)
{% endopenapi-operation %}

***

## ✅ Full Flow Summary

| Step | Action                    | Method | Endpoint                                           |
| ---- | ------------------------- | ------ | -------------------------------------------------- |
| 1    | Get products              | `GET`  | `/products/grouped/`                               |
| 2    | Create quote              | `POST` | `/quotes/`                                         |
| 3    | Wallet payment (external) | –      | –                                                  |
| 4    | Register transaction      | `POST` | `/wallets/{address}/transactions/`                 |
| 5    | Track transaction status  | `GET`  | `/wallets/{address}/transactions/{transactionId}/` |
