# Reference API Documentation

## API Specification

### Endpoints


{% swagger src="/bfp_v1.json" path="/references" method="post" expanded="true" %} 
[bfp_v1.json](/bfp_v1.json) 
{% endswagger %}

{% swagger src="/bfp_v1.json" path="/references" method="get" expanded="true" %} 
[bfp_v1.json](/bfp_v1.json) 
{% endswagger %}



## Business Logic for Reference Validation

Reference Processing Steps

1. Product Validation: The sku is used to find the associated product.
2. Regex Validation: The reference is validated against the regex pattern defined for the product.
3. Reference Lookup: Checks if the reference has already been validated.
4. Create or Return Reference: If not previously validated, creates a new validated reference.

## Error Handling
* **Product Not Found**: Raised if the sku does not correspond to any existing product.
* **Invalid Reference**: Raised if the reference does not pass the regex validation. 

