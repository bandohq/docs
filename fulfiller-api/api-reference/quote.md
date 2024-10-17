# Fiat to Digital Asset Conversion API

## 1. Overview

This document outlines the design and implementation of the Fiat-to-Digital Asset Conversion API.  
The API facilitates converting fiat currencies to digital assets and returns a quoted amount based on   
current exchange rates. It supports configurable spreads and multiple external providers for FX and crypto pricing.

## 2. Objectives

- Allow users to convert fiat currencies into supported digital assets.
- Provide real-time quotes based on exchange rates from selected providers.
- Enable configurable spreads and flexible rate sources.
- Integrate with external providers for fiat and cryptocurrency rates.

## 3. Architecture

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


## API Specification

### Endpoint

- **URL**: `/api/v1/quotes/`
- **Method**: `POST`

### Request Body

```json
{
  "fiat_currency": "string",
  "digital_asset": "string",
  "sku": "string"
}
```

* **fiat_currency**: ISO 4217 code for the fiat currency (e.g., "USD").
* **digital_asset**: Digital asset for conversion (e.g., "USDC").
* **sku**: Product identifier.

Response Body
```json
{
  "fiat_currency": "string",
  "fiat_amount": "string",
  "digital_asset": "string",
  "digital_asset_amount": "string",
  "sku": "string"
}
```
* **fiat_currency**: The fiat currency used.
* **fiat_amount**: The amount in fiat currency.
* **digital_asset**: The digital asset converted to.
* **digital_asset_amount**: The quoted amount in the digital asset.
* **sku**: Product identifier.
