## BandoRouter

## Overview

The `BandoRouter` serves as the main entry point for users to submit fulfillment requests. It routes requests to the appropriate fulfillable contracts, validates service information, and ensures that tokens and amounts meet the required criteria before processing.

The `BandoRouter` is responsible for:
* Validating service IDs and references against the registry
* Calculating fee amounts based on service configurations
* Routing native currency fulfillment requests to the `BandoFulfillable` contract
* Routing ERC20 token fulfillment requests to the `BandoERC20Fulfillable` contract
* Validating that ERC20 tokens are registered in the token registry

The contract follows a versioned inheritance pattern:
* `BandoRouterV1`: Base functionality for request routing
* `BandoRouterV1_1`: Enhanced functionality and optimizations

The `BandoRouter` responsibilities can be broken down into the following concepts:
* [Request Routing](#request-routing)
* [Service Validation](#service-validation)
* [Fee Calculation](#fee-calculation)

## Request Routing

The following methods are called by users to submit fulfillment requests:

#### `requestService`

```solidity
/**
 * @notice Requests a fulfillment using native currency
 * @param serviceId The service identifier
 * @param serviceRef The service reference
 * @param fiatAmount The fiat amount to be charged for the fulfillable
 */
function requestService(uint256 serviceId, string calldata serviceRef, uint256 fiatAmount) external payable
```

Allows a user to request a fulfillment using native currency. The function validates the service information, calculates the fee amount, and forwards the request to the `BandoFulfillable` contract for escrow.

*Effects*:
* Validates the service ID and reference
* Retrieves the service details and fee percentage from the registry
* Calculates the fee amount based on the sent value and fee percentage
* Creates a `FulFillmentRequest` with the caller as the payer
* Calls `IBandoFulfillable(_escrow).escrow(serviceId, request)` to escrow the funds
* Emits `FulfillmentRequested(serviceId, serviceRef, fiatAmount, msg.sender, msg.value)` event

*Requirements*:
* `serviceId` MUST be a valid service ID in the registry
* `serviceRef` MUST be a valid reference for the service
* `fiatAmount` MUST be greater than zero
* Sent value MUST be greater than zero

#### `requestERC20Service`

    string calldata serviceRef,
    uint256 fiatAmount,
    address token,
    uint256 tokenAmount
) external
```
Requests a fulfillment using ERC20 tokens.

### Service Validation

```solidity
function validateService(uint256 serviceId, string calldata serviceRef) public view returns (bool)
```
Validates if a service ID and reference are valid.

### Fee Calculation

```solidity
function calculateFeeAmount(uint256 amount, uint16 feeAmountBasisPoints) public pure returns (uint256)
```
Calculates the fee amount based on the total amount and fee basis points.

## Events

```solidity
event FulfillmentRequested(uint256 indexed serviceId, string serviceRef, uint256 fiatAmount, address payer, uint256 weiAmount)
```
Emitted when a native currency fulfillment is requested.

```solidity
event ERC20FulfillmentRequested(uint256 indexed serviceId, string serviceRef, uint256 fiatAmount, address payer, address token, uint256 tokenAmount)
```
Emitted when an ERC20 token fulfillment is requested.

## Errors

```solidity
error InvalidServiceId(uint256 serviceId)
```
Emitted when an invalid service ID is provided.

```solidity
error InvalidServiceRef(string serviceRef)
```
Emitted when an invalid service reference is provided.

```solidity
error TokenNotRegistered(address token)
```
Emitted when an unregistered token is used for fulfillment.

## Access Control

The router is designed to be used by any user, with validation performed through the service registry:

- Service validation ensures that only valid service IDs and references are processed
- Token validation ensures that only registered tokens are used for ERC20 fulfillments

## Integration Points

- Interacts with `IFulfillableRegistry` to validate services and get fee information
- Interacts with `IERC20TokenRegistry` to validate tokens for ERC20 fulfillments
- Calls `IBandoFulfillable.escrow()` to escrow native currency for fulfillment
- Calls `IBandoERC20Fulfillable.escrow()` to escrow ERC20 tokens for fulfillment
