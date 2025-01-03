# Product Price to Digital Asset Price Quote API

## Overview

This document outlines the design and implementation of the Fiat-to-Digital Asset Conversion API.  
The API facilitates converting fiat currencies to digital assets and returns a quoted amount based on   
current exchange rates. It supports configurable spreads and multiple external providers for FX and crypto pricing.

## Objectives

- Allow users to convert fiat currencies into supported digital assets.
- Provide real-time quotes based on exchange rates from selected providers.
- Enable configurable spreads and flexible rate sources.
- Integrate with external providers for fiat and cryptocurrency rates.

## Architecture

The API involves interaction with various components for currency conversion and quote calculation.

```mermaid
sequenceDiagram
    participant Client
    participant QuoteService
    participant ProductRepository
    participant QuoteFiatProvider
    participant QuoteDigitalProvider

    Client->>QuoteService: create_quote(fiat_currency, digital_asset, sku)
    QuoteService->>ProductRepository: find_product_variation_by_sku(sku)
    ProductRepository-->>QuoteService: Product

    QuoteService->>QuoteDigitalProvider: get_ticker_data(digital_asset_symbol)
    QuoteDigitalProvider-->>QuoteService: price_stable_coin_value

    QuoteService->>QuoteFiatProvider: request_conversion(fiat_amount, fiat_currency, product_fiat_currency)
    QuoteFiatProvider-->>QuoteService: converted_fiat_amount

    QuoteService->>QuoteService: _calculate_digital_asset_amount(converted_fiat_amount, price_stable_coin_value, spread)
    Note right of QuoteService: Applies the spread to adjust the price

    QuoteService-->>Client: Quote (fiat_currency, fiat_amount, digital_asset, digital_asset_amount, sku)
```
