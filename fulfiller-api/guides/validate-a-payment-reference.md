---
description: Technical guide for validating payment references in Bando. Ensure secure transaction processing with our validation endpoints.
---

# Validating Payment References

## Checking Reference Status

### Endpoint

```http
GET /references?validation_id=your-validation-id
```

### Response Example

```json
[
  {
    "id": "123e4567-e89b-12d3-a456-426614174000",
    "validation_id": "VAL123456",
    "status": "VALIDATED",
    "reference": "recipient@email.com",
    "created_at": "2024-02-17T12:00:00Z",
    "transaction_intent": {
      "status": "PENDING",
      "amount": 50.0
    }
  }
]
```

## Reference States

- `PENDING`: Initial validation in progress
- `FAILED`: Validation failed
- `COMPLETED`: Transaction validation completed successfully

## Required Fields by Brand

### LLBean Prepaid Codes

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

### IHOP Prepaid Codes

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
    },
    {
      "name": "recipient.msisdn",
      "valueType": "string"
    },
    {
      "name": "sender.firstName",
      "valueType": "string"
    },
    {
      "name": "sender.lastName",
      "valueType": "string"
    },
    {
      "name": "sender.msisdn",
      "valueType": "string"
    }
  ]
}
```

## Best Practices

### 1. Reference Validation

- Pre-validate using provided regex patterns
- Include all required fields for the brand
- Check product requirements before submission

### 2. Phone Numbers

- Always include country code
- Remove any unnecessary formatting
- Validate against the regex pattern

### 3. Email Addresses

- Validate format before submission
- Check for required recipient fields
- Include all brand-specific fields

### 4. Transaction Intent

- Verify SKU exists for the product
- Use correct chain ID (e.g., 137 for Polygon)
- Specify supported tokens (e.g., USDC)

## Common Use Cases

### 1. eSIM Purchase

```json
{
  "reference": "+14155552671",
  "transaction_intent": {
    "sku": "fa8d9c2f-d10b-4e2b-8f7a-4f89c5f2aa56",
    "amount": 62.0,
    "chain": "137",
    "token": "USDC"
  }
}
```

### 2. Gift Card with All Fields

```json
{
  "reference": "recipient@email.com",
  "required_fields": [
    {
      "name": "recipient.firstName",
      "value": "John"
    },
    {
      "name": "recipient.lastName",
      "value": "Doe"
    },
    {
      "name": "sender.firstName",
      "value": "Jane"
    },
    {
      "name": "sender.lastName",
      "value": "Smith"
    }
  ],
  "transaction_intent": {
    "sku": "1c5afebf-7302-467b-bcaa-d2a3b8b5869f",
    "amount": 50.0,
    "chain": "137",
    "token": "USDC"
  }
}
```

## Tips for Success

1. **Validation Process**

   - Check product requirements first
   - Validate reference format
   - Include all required fields
   - Verify transaction details

2. **Error Prevention**

   - Use correct regex patterns
   - Include brand-specific fields
   - Validate before submission
   - Check reference status

3. **Transaction Details**
   - Use correct chain IDs
   - Specify supported tokens
   - Verify SKU exists
   - Check amount matches SKU
