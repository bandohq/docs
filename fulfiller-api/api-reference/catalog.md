---
description: Get the list of the Bando Fulfiller Catalog.
---

# Product Catalog API

## Overview

This API fetches a paginated list of products available for payment with the bando fulfillment protocol.

## Objectives

The most common use for this API would be to populate a UI that enlists the available services to spend for your wallet / Dapp / game / metaverse, etc.

## API Specification

{% swagger src="../../bfp_v1.json" path="/products" method="get" expanded="true" %}
[bfp_v1.json](../../bfp_v1.json)
{% endswagger %}
