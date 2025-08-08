---
description: >-
  Learn how to fetch and display available products through Bando's web3
  spending API. Implementation guide for accessing our catalog of supported
  services.
---

# Explore our Product Catalog

We built a simple catalog explorer so that you can get started with all the products you can sell on your wallet/Dapp. It the simplest way to explore and get used to the global catalog of over 35k individual skus.

{% embed url="https://catalog.bando.cool" %}

## Before you start

### Required fields

{% hint style="info" %}
Each product variant has a `requiredFields` array.

This fields are needed to create the necessary front on your application when you build the \
checkout flow.
{% endhint %}

#### Example:

```http
"requiredFields": [
  {
    "name": "recipient.firstName",
    "valueType": "string"
  },
  {
    "name": "recipient.lastName",
    "valueType": "string"
  }
]
```

### Product Details to Check

1. **Basic Information**
   * `brandName`: Brand name
   * `brandSlug`: Unique brand identifier
   * `imageUrl`: Brand logo URL
   * `order`: Display order priority
2. **Product Details**
   * `shortNotes`: Quick product description
   * `notes`: Detailed information
   * `price`: Pricing information
   * `sku`: Unique product identifier
3. **Validation Requirements**
   * `referenceType`: Main validation format
   * `requiredFields`: Additional required information
   * `regex`: Validation patterns

## Go to the Catalog Explorer

{% embed url="https://catalog.bando.cool" fullWidth="false" %}
_Bando's simple catalog explorer made in react and vite._
{% endembed %}

