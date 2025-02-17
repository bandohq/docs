# Get Available Products

Learn how to use our Grouped Products API to efficiently get products organized by type and brand. This guide shows you how to retrieve and understand grouped product data.

## Retrieving Grouped Products

Get products organized by type and brand using:
```http
GET /products/grouped
```

### Query Parameters

- `country`: Filter by country code (e.g., "US", "MX")
- `type`: Filter by product type (e.g., "esim", "topup", "giftcard")
- `brand`: Filter by specific brand
- `subType`: Filter by product subtype
- `pageSize`: Number of items per page
- `pageNumber`: Page number to retrieve

Example request:
```http
GET /products/grouped?country=US&type=esim
```

## Understanding the Response

The response groups products first by type, then by brand. Here's what you'll get:

```json
{
  "products": [
    {
      "productType": "esim",
      "brands": [
        {
          "brandName": "eSIM",
          "brandSlug": "esim",
          "imageUrl": "brand-logo-url",
          "order": 10000,
          "variants": [
            {
              "sku": "15fe0419-a328-4a52-afae-7651ad8c2362",
              "price": {
                "fiatCurrency": "USD",
                "fiatValue": "16.00"
              },
              "shortNotes": "eSIM 30 Days, 10 GB",
              "notes": "eSIM United States of America (the) No Roaming"
            }
          ]
        }
      ]
    }
  ]
}
```

### Examples by Product Type

#### 1. eSIM Products
```json
{
  "sku": "fa8d9c2f-d10b-4e2b-8f7a-4f89c5f2aa56",
  "shortNotes": "eSIM 30 Days, 50 GB",
  "price": {
    "fiatCurrency": "USD",
    "fiatValue": "62.00"
  },
  "notes": "eSIM United States of America (the) No Roaming"
}
```

#### 2. Top-up Products
```json
{
  "sku": "835e984d-1f1d-4492-a714-595d36d0f6c6",
  "shortNotes": "AT&T $35 PIN",
  "price": {
    "fiatCurrency": "USD",
    "fiatValue": "35.00"
  },
  "brand": "at-t-pin-usa",
  "notes": "AT&T $35 PIN"
}
```

## Common Use Cases

### 1. Get All eSIMs for a Country
```http
GET /products/grouped?country=US&type=esim
```
This shows all available eSIM options, organized by brand.

### 2. View All Top-up Options
```http
GET /products/grouped?type=topup
```
See all mobile top-up products grouped by carrier.

### 3. Find Products by Brand
```http
GET /products/grouped?brand=at-t-pin-usa
```
Shows all AT&T products organized by type.

## Product Details to Look For

1. For eSIMs:
   - Duration (e.g., "30 Days")
   - Data amount (e.g., "50 GB")
   - Speed limits (if any)
   - Coverage information

2. For Top-ups:
   - Carrier information
   - Denomination amount
   - Any special conditions

## Tips for Using Grouped Products

1. **Filter Efficiently**
   - Use the country parameter to see relevant products
   - Combine type and brand for specific searches

2. **Check Product Details**
   - Always look at both `shortNotes` and `notes`
   - Verify prices in both fiat and stable coins if needed

3. **Understanding Variants**
   - Each brand can have multiple product variants
   - Variants might have different durations or data amounts
   - Look for the best value by comparing similar variants

4. **Price Information**
   - Prices are available in both fiat and stable coins
   - Check `fiatValue` for local currency pricing
   - Use `stableCoinValue` for crypto payments

