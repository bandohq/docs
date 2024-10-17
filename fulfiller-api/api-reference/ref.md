# Reference API Documentation

## API Specification

### Endpoints

### Endpoint

{% swagger src="/bfp_v1.json" path="/references" expanded="true" %} 
[bfp_v1.json](/bfp_v1.json) 
{% endswagger %}


* **error**: Error message if any, otherwise an empty string.
* **message**: Status message indicating the result of the operation.
* **data**: Contains the validated reference information:
* **reference**: The validated payment reference.
* **reference_type**: Type of the reference.
* **status**: Status of the reference (e.g., "VALIDATED").
* **reference_required_fields**: Additional fields required for the reference, provided as key-value pairs.


**Error Responses**
- **400 Bad Request**: If the reference does not match the expected regex pattern.
- **404 Not Found**: If the product is not found by the provided SKU.


## Retrieve a Validated Reference
- URL: /api/v1/references/{id}/
- Method: GET 


**Response Body**
```json

{
  "error": "",
  "message": "Reference retrieved successfully.",
  "data": {
    "reference": "string",
    "reference_type": "string",
    "status": "string",
    "reference_required_fields": {
      "field_name_1": "value_1",
      "field_name_2": "value_2"
    }
  }
}
```

* error: Error message if any, otherwise an empty string.
* message: Status message indicating the retrieval result.
* data: Contains the validated reference information as described above.

**Error Responses**

404 Not Found: If the reference is not found for the given ID.

## Business Logic for Reference Validation

Reference Processing Steps

1. Product Validation: The sku is used to find the associated product.
2. Regex Validation: The reference is validated against the regex pattern defined for the product.
3. Reference Lookup: Checks if the reference has already been validated.
4. Create or Return Reference: If not previously validated, creates a new validated reference.

## Error Handling
* **Product Not Found**: Raised if the sku does not correspond to any existing product.
* **Invalid Reference**: Raised if the reference does not pass the regex validation. 

