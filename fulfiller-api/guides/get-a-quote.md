# Price Quote API Guide

Learn how to get price quotes for converting fiat currency to digital assets for your purchases.

## Getting a Price Quote

The Quote API helps you get current prices and fees for purchasing products using digital assets.

### Endpoint
```http
POST /quotes
```

### Request Format

```json
{
    "sku": "ATT_MX_10-I",
    "fiat_currency": "MXN",
    "digital_asset": "USDC",
    "chain_id": 137
}
```

Field descriptions:
- `sku`: Product identifier (e.g., "ATT_MX_10-I" )
- `fiat_currency`: Local currency code (e.g., "MXN", "USD")
- `digital_asset`: Digital token symbol (e.g., "USDC", "USDT")
- `chain_id`: Blockchain network ID (e.g., 137 for Polygon)

### Response Example

```json
{
    "error": "",
    "message": "Quote created successfully.",
    "data": {
        "fiat_currency": "MXN",
        "fiat_amount": "10.00",
        "digital_asset": "USDC",
        "digital_asset_amount": "0.58",
        "total_amount": "0.60",
        "sku": "ATT_MX_10-I"
    }
}
```

## Common Use Cases

### 1. Mobile Top-up Quote
```json
{
    "sku": "TELCEL_MX_100-I",
    "fiat_currency": "MXN",
    "digital_asset": "USDC",
    "chain_id": 137
}
```

### 2. Gift Card Quote
```json
{
    "sku": "AMAZON_MX_500-I",
    "fiat_currency": "MXN",
    "digital_asset": "USDT",
    "chain_id": 137
}
```

### 3. International eSIM Quote
```json
{
    "sku": "ESIM_US_30D-I",
    "fiat_currency": "USD",
    "digital_asset": "USDC",
    "chain_id": 1
}
```


## Understanding the Quote

The quote response includes:
1. **Original Price**
   - `fiat_currency`: Local currency (MXN, USD, etc.)
   - `fiat_amount`: Price in local currency

2. **Converted Amount**
   - `digital_asset`: Token used (USDC, USDT, etc.)
   - `digital_asset_amount`: Base price in tokens


## Best Practices

1. **Currency Conversion**
   - Rates update in real-time
   - Final amounts may vary slightly
   - Always use latest quote for transactions

2. **SKU Format**
   - Use correct SKU format: BRAND_COUNTRY_AMOUNT-I
   - Check SKU exists
   - Verify product availability

