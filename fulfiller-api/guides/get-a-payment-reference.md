# Payment Reference Guide

Learn how to handle payment references for transactions. 

# Creating Payment References

## What is a Payment Reference?

A payment reference is the delivery information for a product:
- Phone numbers for mobile top-ups and eSIMs
- Email addresses for gift cards

Each product type has specific reference requirements and validation rules.

## Creating a Reference

### Endpoint
```http
POST /references
```

### Request Format by Product Type

#### 1. Mobile Top-ups and eSIMs
```json
{
  "reference": "+14155552671",
  "transaction_intent": {
    "sku": "ATT_MX_10-I",
    "quantity": 1,
    "amount": 10.00,
    "chain": "pol",
    "token": "USDC", 
     "wallet": "0x123.."
  }
}
```

Reference requirements:
- Must match phone regex: `^(\\+?\\d{1,3}[-.\\s]?)?(\\(?\\d{3}\\)?[-.\\s]?)?\\d{3}[-.\\s]?\\d{4}$`
- Include country code
- No special characters 
- Some products require extra fields, check the required_fields of the product endpoint

#### 2. Gift Cards
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
    }
  ],
  "transaction_intent": {
    "sku": "09574598-545b-4fc2-a65a-e00ebe543551",
    "amount": 50.00,
    "chain": "137",
    "token": "USDC"
  }
}
```

Reference requirements:
- Must match email regex: `^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}$`
- Required fields vary by brand
- Some brands require sender information

### Response Format
```json
{
  "id": "uuid",
  "validation_id": "string",
  "reference_type": "email",
  "status": "PENDING",
  "reference": "recipient@email.com",
  "created_at": "2024-02-17T12:00:00Z",
  "updated_at": "2024-02-17T12:00:00Z",
  "transaction_intent": {
    "id": "uuid",
    "sku": "0000000-545b-4fc2-a65a-000000000",
    "amount": 50.00,
    "status": "PENDING"
  }
}
```
