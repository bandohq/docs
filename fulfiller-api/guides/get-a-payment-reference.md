# Payment Reference Validation Guide

Learn how to validate payment references for your transactions. This guide explains how to create, validate, and manage payment references securely.

## What is a Payment Reference?

A payment reference is any identifier needed to complete a transaction, such as:
- Phone numbers 
- Email addresses 


## Creating a Reference

### Endpoint
```http
POST /references
```

### Request Format
```json
{
  "reference": "string",
  "required_fields": [
    {
      "name": "string",
      "value": "string"
    }
  ],
  "transaction_intent": {
    "sku": "product-sku",
    "quantity": 1,
    "amount": 10.00,
    "chain": "ethereum",
    "wallet": "0x...",
    "token": "USDC"
  }
}
```

### Example Requests

1. **Mobile Top-up Reference**
```json
{
  "reference": "+1234567890",
  "transaction_intent": {
    "sku": "telcel-mx-100",
    "quantity": 1,
    "amount": 100.00,
    "chain": "polygon",
    "wallet": "0x123...",
    "token": "USDC"
  }
}
```

2. **Gaming Credit Reference**
```json
{
  "reference": "gamer123",
  "required_fields": [
    {
      "name": "server",
      "value": "NA-East"
    }
  ],
  "transaction_intent": {
    "sku": "game-credits-1000",
    "amount": 50.00,
    "chain": "bsc",
    "token": "BUSD"
  }
}
```

### Response Format
```json
{
  "id": "uuid",
  "validation_id": "string",
  "reference_type": "phone",
  "status": "PENDING",
  "reference": "string",
  "created_at": "2024-02-17T12:00:00Z",
  "updated_at": "2024-02-17T12:00:00Z",
  "transaction_intent": {
    "id": "uuid",
    "sku": "string",
    "amount": 10.00,
    "status": "PENDING"
  }
}
```

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
    "reference": "+1234567890",
    "created_at": "2024-02-17T12:00:00Z",
    "transaction_intent": {
      "status": "PENDING",
      "amount": 100.00
    }
  }
]
```

## Understanding Reference States

References can be in one of these states:
- `PENDING`: Initial validation in progress
- `FAILED`: Validation failed
- `COMPLETED`: Transaction validation completed successfully

## Best Practices

1. **Reference Format**
   - Validate format client-side before submission
   - Follow the specific format required by each service
   - Include country codes for phone numbers

2. **Security**
   - Never store references in plain text
   - Use HTTPS for all API calls
   - Handle errors gracefully

3. **Transaction Intent**
   - Always include complete transaction details
   - Specify the correct blockchain and token
   - Check amounts and SKUs

## Common Use Cases

### 1. Mobile Top-up Flow
```json
{
  "reference": "+525512345678",
  "transaction_intent": {
    "sku": "telcel-mx-100",
    "amount": 100.00,
    "chain": "polygon",
    "token": "USDC"
  }
}
```

### 2. Digital Gift Card Delivery
```json
{
  "reference": "recipient@email.com",
  "transaction_intent": {
    "sku": "amazon-gift-50",
    "amount": 50.00,
    "chain": "ethereum",
    "token": "USDT"
  }
}
```


## Tips for Success

1. **Validation Speed**
   - References are typically validated within seconds
   - Set appropriate timeouts in your implementation
   - Always check the status before proceeding

2. **Required Fields**
   - Some services need additional information
   - Check product documentation for required fields
   - Include all required fields to avoid validation failures

3. **Reference Privacy**
   - References are stored securely off-chain
   - Only reference IDs are stored on-chain
   - Use validation IDs for status checks

4. **Transaction Intent**
   - Include complete transaction details
   - Verify token and chain compatibility
   - Check amounts and quantities
