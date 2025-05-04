## FulfillmentRequestLib

| File | Notes |
| -------- | -------- |
| [`FulfillmentRequestLib.sol`](../../contracts/libraries/FulfillmentRequestLib.sol) | Library for validating fulfillment requests and calculating fees |

## Overview

The `FulfillmentRequestLib` library provides utility functions for validating fulfillment requests and calculating fees. It is used by the `BandoRouter` to ensure that requests meet the required criteria before being forwarded to the fulfillable contracts.

The library is responsible for:
* Validating that fulfillment requests have valid amounts and references
* Checking that ERC20 tokens are whitelisted in the token registry
* Calculating service fees and swap fees based on configured basis points

The `FulfillmentRequestLib` responsibilities can be broken down into the following concepts:
* [Request Validation](#request-validation)
* [Fee Calculation](#fee-calculation)

## Request Validation

The following methods are used to validate fulfillment requests:

#### `validateRequest`

```solidity
/**
 * @notice validateRequest
 * @dev It checks if the amount sent is greater than zero, if the fiat amount is greater than zero,
 * @param serviceID the product/service ID
 * @param request a valid FulFillmentRequest
 * @param fulfillableRegistry the registry address
 */
function validateRequest(
    uint256 serviceID,
    FulFillmentRequest memory request,
    address fulfillableRegistry
) internal view returns (Service memory)
```

Validates a native currency fulfillment request by checking that the amount sent is greater than zero, the fiat amount is greater than zero, and the service reference is valid. Returns the service details if the request is valid.

*Effects*:
* Checks if the sent value is greater than zero
* Checks if the fiat amount is greater than zero
* Retrieves the service details from the registry
* Validates that the service reference is valid
* Returns the service details

*Requirements*:
* `msg.value` MUST be greater than zero
* `request.fiatAmount` MUST be greater than zero
* `request.serviceRef` MUST be a valid reference for the service in the registry

#### `validateERC20Request`

```solidity
/**
 * @notice validateERC20Request
 * @dev It checks if the token amount sent is greater than zero, if the fiat amount is greater than zero,
 * if the service reference is valid and returns the service
 * @dev We will change the way we handle fees so this validation is prone to change.
 * @param serviceID the product/service ID
 * @param request a valid FulFillmentRequest
 * @param fulfillableRegistry the registry address
 * @param tokenRegistry the token registry address
 */
function validateERC20Request(
  uint256 serviceID,
  ERC20FulFillmentRequest memory request,
  address fulfillableRegistry,
  address tokenRegistry
) internal view returns (Service memory)
```

Validates an ERC20 token fulfillment request by checking that the token amount is greater than zero, the fiat amount is greater than zero, the token is whitelisted, and the service reference is valid. Returns the service details if the request is valid.

*Effects*:
* Checks if the token amount is greater than zero
* Checks if the fiat amount is greater than zero
* Checks if the token is whitelisted in the token registry
* Retrieves the service details from the registry
* Validates that the service reference is valid
* Returns the service details

*Requirements*:
* `request.tokenAmount` MUST be greater than zero
* `request.fiatAmount` MUST be greater than zero
* `request.token` MUST be whitelisted in the token registry
* `request.serviceRef` MUST be a valid reference for the service in the registry

## Fee Calculation

The following method is used to calculate fees for fulfillment requests:

#### `calculateFees`

```solidity
/**
 * @notice calculateFees: Gets service fee and swap fee (if any)
 * and calculates the fee based on the configured fees.
 * @dev Fees are represented in basis points to work with integers
 * on fee percentages below 1%
 * The fee is also rounded up to the nearest integer.
 * This is to avoid rounding errors when calculating the total amount.
 * And to avoid underpaying.
 * totalFee = (amount * basisPoints + 9999) / 10000
 * totalAmount = amount + serviceFee + swapFee
 * @param fulfillableRegistry Service registry contract address
 * @param tokenRegistry Token registry contract address
 * @param serviceID Service/product ID
 * @param tokenAddress Token address (zero address for native coin)
 * @param amount The amount to calculate the fees for
 */
function calculateFees(
    address fulfillableRegistry,
    address tokenRegistry,
    uint256 serviceID,
    address tokenAddress,
    uint256 amount
) internal view returns (uint256 serviceFeeAmount)
```

Calculates the service fee and swap fee (if any) based on the configured fee basis points. The fees are calculated as a percentage of the amount and rounded up to the nearest integer to avoid underpaying.

*Effects*:
* Retrieves the service fee basis points from the registry
* Calculates the service fee as `(amount * feeBasisPoints + 9999) / 10000`
* Retrieves the swap fee basis points from the token registry (if applicable)
* Calculates the swap fee as `(amount * swapFeeBasisPoints + 9999) / 10000` (if applicable)
* Returns the total fee (service fee + swap fee)

## Errors

```solidity
error InsufficientAmount()
```
Emitted when the amount sent is zero.

```solidity
error InvalidFiatAmount()
```
Emitted when the fiat amount is zero.

```solidity
error InvalidRef()
```
Emitted when the service reference is not valid in the registry.

```solidity
error OverflowError()
```
Emitted when an overflow occurs during fee calculation.

```solidity
error AmountMismatch()
```
Emitted when fee amount validations fail.

```solidity
error UnsupportedToken(address token)
```
Emitted when the token is not whitelisted in the token registry.

---
