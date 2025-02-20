---
description: "Learn how to fetch and display available products through Bando's Fulfiller API. Implementation guide for accessing our catalog of supported services."
---

# Products API Guide

Learn how to use our Catalog API to get products organized by type and brand, including product details and validation requirements.

## Getting Products

Retrieve organized products using:

```http
GET /products/grouped
```

### Query Parameters

- `country`: Filter by country code (e.g., "US", "MX")
- `type`: Filter by product type (e.g., "esim", "topup", "gift_card")
- `brand`: Filter by specific brand
- `subType`: Filter by product subtype
- `pageSize`: Number of items per page
- `pageNumber`: Page number to retrieve

## Product Types and Examples

### 1. eSIM Products

Example response:

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
              "sku": "fa8d9c2f-d10b-4e2b-8f7a-4f89c5f2aa56",
              "shortNotes": "eSIM 30 Days, 50 GB",
              "notes": "eSIM United States of America (the) No Roaming",
              "price": {
                "fiatCurrency": "USD",
                "fiatValue": "62.00"
              },
              "referenceType": {
                "name": "phone",
                "valueType": "string",
                "regex": "^(\\+?\\d{1,3}[-.\\s]?)?(\\(?\\d{3}\\)?[-.\\s]?)?\\d{3}[-.\\s]?\\d{4}$"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

Key eSIM details:

- Duration and data info in `shortNotes`
- Coverage details in `notes`
- Phone validation required for delivery
- Various data packages available

### 2. Gift Card Products

Example response:

```json
{
  "products": [
    {
      "productType": "gift_card",
      "brands": [
        {
          "brandName": "LLBean",
          "brandSlug": "llbean",
          "imageUrl": "brand-logo-url",
          "order": 10000,
          "variants": [
            {
              "sku": "09574598-545b-4fc2-a65a-e00ebe543551",
              "shortNotes": "LLBean Gift Card $50",
              "price": {
                "fiatCurrency": "USD",
                "fiatValue": "50.00"
              },
              "referenceType": {
                "name": "email",
                "valueType": "string",
                "regex": "^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}$"
              },
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
            }
          ]
        }
      ]
    }
  ]
}
```

Key gift card details:

- Brand and denomination in `shortNotes`
- Email delivery validation
- Recipient information required
- Various denominations available

### 3. Top-up Products

Example response:

```json
{
  "products": [
    {
      "productType": "topup",
      "brands": [
        {
          "brandName": "AT&T PIN USA",
          "brandSlug": "at-t-pin-usa",
          "imageUrl": "brand-logo-url",
          "order": 10000,
          "variants": [
            {
              "sku": "835e984d-1f1d-4492-a714-595d36d0f6c6",
              "shortNotes": "AT&T $35 PIN",
              "notes": "AT&T $35 PIN",
              "price": {
                "fiatCurrency": "USD",
                "fiatValue": "35.00"
              },
              "referenceType": {
                "name": "phone",
                "valueType": "string",
                "regex": "^(\\+?\\d{1,3}[-.\\s]?)?(\\(?\\d{3}\\)?[-.\\s]?)?\\d{3}[-.\\s]?\\d{4}$"
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## Required Fields by Product Type

### 1. Reference Types

Each product requires specific validation:

- **Email Format (Gift Cards)**

  ```json
  {
    "referenceType": {
      "name": "email",
      "regex": "^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}$"
    }
  }
  ```

- **Phone Format (eSIMs & Top-ups)**
  ```json
  {
    "referenceType": {
      "name": "phone",
      "regex": "^(\\+?\\d{1,3}[-.\\s]?)?(\\(?\\d{3}\\)?[-.\\s]?)?\\d{3}[-.\\s]?\\d{4}$"
    }
  }
  ```

### 2. Additional Fields

Some products require extra information:

```json
{
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
}
```

## Product Details to Check

1. **Basic Information**

   - `brandName`: Provider name
   - `brandSlug`: Unique brand identifier
   - `imageUrl`: Brand logo URL
   - `order`: Display order priority

2. **Variant Details**

   - `shortNotes`: Quick product description
   - `notes`: Detailed information
   - `price`: Pricing information
   - `sku`: Unique product identifier

3. **Validation Requirements**
   - `referenceType`: Main validation format
   - `requiredFields`: Additional required information
   - `regex`: Validation patterns

## Common Use Cases

### 1. Get eSIMs with Data Details

```http
GET /products/grouped?type=esim&country=US
```

Shows eSIMs with data packages and duration.

### 2. View Gift Card Options

```http
GET /products/grouped?type=gift_card&brand=llbean
```

Displays available gift card denominations.

### 3. Find Top-up Products

```http
GET /products/grouped?type=topup&country=US
```

Lists available mobile top-up options.

## Best Practices

1. **Product Selection**

   - Check `shortNotes` for quick info
   - Read `notes` for full details
   - Verify price and currency
   - Look at brand information

2. **Validation Handling**

   - Pre-validate reference formats
   - Include all required fields
   - Follow regex patterns
   - Handle validation errors

3. **Display Information**
   - Use brand images when available
   - Show ordered by priority
   - Display clear pricing
   - Include important notes
