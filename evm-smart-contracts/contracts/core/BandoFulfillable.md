## BandoFulfillable

| File | Notes |
| -------- | -------- |
| [`BandoFulfillableV1.sol`](../../contracts/BandoFulfillableV1.sol) | Initial version with core functionality |
| [`BandoFulfillableV1_1.sol`](../../contracts/BandoFulfillableV1_1.sol) | Enhanced version with optimizations |
| [`BandoFulfillableV1_2.sol`](../../contracts/BandoFulfillableV1_2.sol) | Latest version with stablecoin swapping |
| [`IBandoFulfillable.sol`](../../contracts/IBandoFulfillable.sol) | Base interface |
| [`IBandoFulfillableV1_2.sol`](../../contracts/IBandoFulfillableV1_2.sol) | Interface for V1_2 |

## Overview

The `BandoFulfillable` contract manages the escrow and distribution of native currency for fulfillment services. It holds funds in escrow until fulfillment results are registered, then manages the release of funds to beneficiaries and fee accumulation.

The `BandoFulfillable` is responsible for:
* Accepting native currency for fulfillment requests and holding it in escrow
* Tracking fulfillment records and their statuses
* Managing releaseable pools for successful fulfillments
* Managing accumulated fees for service providers
* In V1_2, supporting swapping of native currency to stablecoins using DEX aggregators

The contract follows a versioned inheritance pattern:
* `BandoFulfillableV1`: Base functionality for native currency escrow and fulfillment
* `BandoFulfillableV1_1`: Enhanced functionality and optimizations
* `BandoFulfillableV1_2`: Added support for stablecoin swapping via DEX aggregators

The `BandoFulfillable` responsibilities can be broken down into the following concepts:
* [Escrow Management](#escrow-management)
* [Fulfillment Registration](#fulfillment-registration)
* [Fund Management](#fund-management)
* [Stablecoin Swapping](#stablecoin-swapping)

## Escrow Management

The following methods are used to manage the escrow of native currency for fulfillment requests:

#### `deposit`

```solidity
/**
 * @notice Deposits funds for a service request
 * @param serviceID The ID of the service
 * @param request The fulfillment request details
 * @param feeAmount The fee amount for the service
 */
function deposit(
    uint256 serviceID,
    FulFillmentRequest memory request,
    uint256 feeAmount
) external payable
```

Deposits native currency for a fulfillment request. The function validates the request, uses the provided fee amount, and creates a new fulfillment record.

*Effects*:
* Validates that the caller is the router
* Creates a new fulfillment record
* Tracks the deposit amount for the payer
* Emits `DepositReceived` event

*Requirements*:
* Caller MUST be the router
* Sent value MUST match the required amount

## Fulfillment Registration

The following methods are used to register fulfillment results:

#### `registerFulfillment`

```solidity
/**
 * @notice Registers a fulfillment result and updates the appropriate pools based on the result status
 * @param serviceID The service identifier
 * @param fulfillment The fulfillment result attached to it
 * @return Returns true if the registration was successful
 */
function registerFulfillment(uint256 serviceID, FulFillmentResult memory fulfillment) public virtual nonReentrant returns (bool)
```

Registers a fulfillment result and updates the appropriate pools based on the result status. The function validates the fulfillment result and updates the fulfillment record status.

*Effects*:
* Validates the fulfillment record exists
* Updates the fulfillment record status
* If status is SUCCESS:
  * Adds the fulfillment amount to the releaseable pool
  * Adds the fee amount to accumulated fees
  * Updates the fulfillment record with receipt URI and external ID
* If status is FAILED, authorizes a refund to the payer
* Subtracts the total amount from the payer's deposits

*Requirements*:
* Caller MUST be the manager
* Fulfillment record MUST exist
* Fulfillment record status MUST be PENDING
* Fulfillment status MUST be either SUCCESS or FAILED

## Fund Management

The following methods are used to manage funds after fulfillment:

#### `beneficiaryWithdraw`

```solidity
/**
 * @notice Withdraws the beneficiary's available balance to release (fulfilled with success)
 * @param serviceID The service identifier
 */
function beneficiaryWithdraw(uint256 serviceID) public virtual nonReentrant
```

Withdraws the beneficiary's available balance to release (fulfilled with success). Only the manager can call this function.

*Effects*:
* Validates the caller is the manager
* Retrieves the service details from the registry
* Transfers the releaseable pool amount to the beneficiary
* Resets the releaseable pool for the service

*Requirements*:
* Caller MUST be the manager
* Releaseable pool MUST have a non-zero balance

#### `withdrawAccumulatedFees`

```solidity
/**
 * @notice Withdraws the accumulated fees for a given service ID
 * @param serviceId The service identifier
 */
function withdrawAccumulatedFees(uint256 serviceId) external nonReentrant
```

Withdraws the accumulated fees for a given service ID. Only the manager can call this function.

*Effects*:
* Validates the caller is the manager
* Retrieves the service details from the registry
* Transfers the accumulated fees to the beneficiary
* Resets the accumulated fees for the service
* Emits `FeesWithdrawn` event

*Requirements*:
* Caller MUST be the manager
* Accumulated fees MUST have a non-zero balance

## Stablecoin Swapping

The following methods are available in V1_2 for swapping native currency to stablecoins:

#### `swapPoolsToStable`

```solidity
/**
 * @dev Swaps both releaseable pool and accumulated fees to stablecoins in a single transaction
 * using an off-chain generated Dex aggregator call.
 * 
 * Requirements:
 * - Only the manager can call this.
 * - A Dex aggregator address must be whitelisted.
 * - The fromToken must have sufficient combined balance.
 * 
 * @param serviceId The service identifier.
 * @param recordId The fulfillment record identifier.
 * @param swapData The struct capturing the aggregator call data, tokens, and amounts.
 */
function swapPoolsToStable(uint256 serviceId, uint256 recordId, SwapData calldata swapData) external nonReentrant onlyManager
```

Swaps both releaseable pool and accumulated fees to stablecoins in a single transaction using an off-chain generated DEX aggregator call.

*Effects*:
* Retrieves the fulfillment record
* Calls `SwapNativeLib.swapNativeToStable` to perform the swap
* Updates the stablecoin releaseable pools and accumulated fees

*Requirements*:
* Caller MUST be the manager
* Fulfillment record MUST exist

#### `beneficiaryWithdrawStable`

```solidity
/**
 * @dev Withdraws the beneficiary's available balance to release (fulfilled with success).
 * Only the manager can withdraw the releaseable pool.
 * @param serviceId The service identifier.
 * @param token The token address.
 */
function beneficiaryWithdrawStable(uint256 serviceId, address token) public onlyManager nonReentrant
```

Withdraws the beneficiary's available stablecoin balance. Only the manager can call this function.

*Effects*:
* Retrieves the service details from the registry
* Transfers the stablecoin releaseable pool amount to the beneficiary
* Resets the stablecoin releaseable pool for the service and token

*Requirements*:
* Caller MUST be the manager
* Stablecoin releaseable pool MUST have a non-zero balance

#### `withdrawAccumulatedFeesStable`

```solidity
/**
 * @dev Withdraws the accumulated fees for a given service ID.
 * Only the manager can withdraw the accumulated fees.
 * @param serviceId The service identifier.
 * @param token The token address.
 */
function withdrawAccumulatedFeesStable(uint256 serviceId, address token) external onlyManager nonReentrant
```

Withdraws the accumulated stablecoin fees for a given service ID. Only the manager can call this function.

*Effects*:
* Retrieves the service details from the registry
* Transfers the accumulated stablecoin fees to the beneficiary
* Resets the accumulated stablecoin fees for the service and token

*Requirements*:
* Caller MUST be the manager
* Accumulated stablecoin fees MUST have a non-zero balance

#### `subtractPoolsAndFees`

```solidity
/**
 * @dev Resets the releaseable pools and accumulated fees for a given service and token.
 * @param serviceId The service identifier.
 * @param token The token address.
 * @param amount The amount to subtract.
 * @param fees The fees to subtract.
 */
function subtractPoolsAndFees(uint256 serviceId, address token, uint256 amount, uint256 fees) external onlyManager
```

Subtracts the releaseable pools and accumulated fees for a given service and token. This is used when accumulating funds in the fulfillment manager.

*Effects*:
* Subtracts the specified amount from the stablecoin releaseable pool
* Subtracts the specified fees from the stablecoin accumulated fees
* Emits `PoolsAndFeesSubtracted` event

*Requirements*:
* Caller MUST be the manager

#### `withdrawFulfillerPoolAndFees`

```solidity
/**
 * @dev Withdraws the fulfiller's pool and fees.
 * @param token The token address.
 * @param amount The amount to withdraw.
 * @param fees The fees to withdraw.
 * @param beneficiary The beneficiary address.
 * @param feesBeneficiary The fees beneficiary address.
 */
function withdrawFulfillerPoolAndFees(
    address token,
    uint256 amount,
    uint256 fees,
    address beneficiary,
    address feesBeneficiary
) external onlyManager nonReentrant
```

Withdraws the fulfiller's pool and fees to the specified beneficiaries.

*Effects*:
* Validates the token and beneficiary addresses
* Transfers the specified amount to the beneficiary
* Transfers the specified fees to the fees beneficiary
* Emits `FulfillerPoolAndFeesWithdrawn` event

*Requirements*:
* Caller MUST be the manager
* Token address MUST NOT be zero
* Beneficiary and fees beneficiary addresses MUST NOT be zero
* Contract MUST have sufficient token balance

## Events

```solidity
event DepositReceived(FulFillmentRecord record)
```
Emitted when a deposit is received.

```solidity
event FulfillmentRegistered(uint256 indexed serviceId, uint256 indexed recordId, FulFillmentResult result)
```
Emitted when a fulfillment result is registered.

```solidity
event BeneficiaryWithdrawn(uint256 indexed serviceId, address indexed beneficiary, uint256 amount)
```
Emitted when funds are withdrawn to a beneficiary.

```solidity
event FeesWithdrawn(uint256 indexed serviceId, address beneficiary, uint256 amount)
```
Emitted when fees are withdrawn.

```solidity
event PoolsAndFeesSubtracted(uint256 serviceId, address token, uint256 amount, uint256 fees)
```
Emitted when pools and fees are subtracted.

## Errors

```solidity
error InvalidAddress(address address_)
```
Emitted when an invalid address is provided.

```solidity
error InvalidManager(address manager)
```
Emitted when the caller is not the authorized manager.

```solidity
error InvalidRouter(address router_)
```
Emitted when the router address is invalid.

```solidity
error NoBalanceToRelease()
```
Emitted when there is no balance to release.

```solidity
error NoFeesToWithdraw()
```
Emitted when there are no fees to withdraw.

```solidity
error InsufficientBalance(uint256 required, uint256 balance)
```
Emitted when a balance is insufficient for an operation.

## Parameterization

* `_manager`: Address of the fulfillment manager
* `_router`: Address of the router
* `_fulfillableRegistry`: Address of the fulfillable registry
* `_releaseablePool`: Mapping of service IDs to their releaseable pool amounts
* `_accumulatedFees`: Mapping of service IDs to their accumulated fees
* `_stableReleasePools`: Mapping of service IDs and token addresses to their stablecoin releaseable pool amounts (V1_2)
* `_stableAccumulatedFees`: Mapping of service IDs and token addresses to their stablecoin accumulated fees (V1_2)

---
