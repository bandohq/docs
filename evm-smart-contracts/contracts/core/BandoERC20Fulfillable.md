## BandoERC20Fulfillable

| File | Notes |
| -------- | -------- |
| [`BandoERC20FulfillableV1.sol`](../../contracts/BandoERC20FulfillableV1.sol) | Initial version with core functionality |
| [`BandoERC20FulfillableV1_1.sol`](../../contracts/BandoERC20FulfillableV1_1.sol) | Enhanced version with optimizations |
| [`BandoERC20FulfillableV1_2.sol`](../../contracts/BandoERC20FulfillableV1_2.sol) | Latest version with stablecoin swapping |
| [`IBandoERC20Fulfillable.sol`](../../contracts/IBandoERC20Fulfillable.sol) | Base interface |
| [`IBandoERC20FulfillableV1_1.sol`](../../contracts/IBandoERC20FulfillableV1_1.sol) | Interface for V1_1 |
| [`IBandoERC20FulfillableV1_2.sol`](../../contracts/IBandoERC20FulfillableV1_2.sol) | Interface for V1_2 |

## Overview

The `BandoERC20Fulfillable` contract manages the escrow and distribution of ERC20 tokens for fulfillment services. It holds tokens in escrow until fulfillment results are registered, then manages the release of tokens to beneficiaries and fee accumulation.

The `BandoERC20Fulfillable` is responsible for:
* Accepting ERC20 tokens for fulfillment requests and holding them in escrow
* Tracking fulfillment records and their statuses
* Managing releaseable pools for successful fulfillments
* Managing accumulated fees for service providers
* In V1_2, supporting swapping of tokens to stablecoins using DEX aggregators

The contract follows a versioned inheritance pattern:
* `BandoERC20FulfillableV1`: Base functionality for ERC20 token escrow and fulfillment
* `BandoERC20FulfillableV1_1`: Enhanced functionality and optimizations
* `BandoERC20FulfillableV1_2`: Added support for stablecoin swapping via DEX aggregators

The `BandoERC20Fulfillable` responsibilities can be broken down into the following concepts:
* [Escrow Management](#escrow-management)
* [Fulfillment Registration](#fulfillment-registration)
* [Fund Management](#fund-management)
* [Stablecoin Swapping](#stablecoin-swapping)

## Escrow Management

The following methods are used to manage the escrow of ERC20 tokens for fulfillment requests:

#### `depositERC20`

```solidity
/**
 * @notice Deposits ERC20 tokens for a service request
 * @param serviceID The ID of the service
 * @param request The ERC20 fulfillment request details
 * @param feeAmount The fee amount for the service
 */
function depositERC20(uint256 serviceID, ERC20FulFillmentRequest memory request, uint256 feeAmount) external
```

Deposits ERC20 tokens for a fulfillment request. The function validates the request, uses the provided fee amount, and transfers the tokens from the sender to the contract.

*Effects*:
* Validates that the caller is the router
* Creates a new fulfillment record with the request details
* Transfers the tokens from the sender to the contract using `SafeERC20.safeTransferFrom`
* Tracks the deposit amount for the payer
* Emits `ERC20DepositReceived` event with the record details

*Requirements*:
* Caller MUST be the router
* Sender MUST have approved the contract to transfer at least the required token amount

## Fulfillment Registration

The following methods are used to register fulfillment results:

#### `registerFulfillment`

```solidity
/**
 * @notice Registers a fulfillment for a service
 * @param serviceID The ID of the service
 * @param fulfillment The fulfillment result
 * @return bool Indicating if the registration was successful
 */
function registerFulfillment(uint256 serviceID, FulFillmentResult memory fulfillment) external returns (bool)
```

Registers a fulfillment result and updates the appropriate pools based on the result status.

*Effects*:
* Validates the fulfillment record exists
* Updates the fulfillment record status
* If status is SUCCESS, adds the token amount to the releaseable pool and fee amount to accumulated fees
* If status is FAILED, authorizes a refund to the payer

*Requirements*:
* Caller MUST be the manager
* Fulfillment record MUST exist
* Fulfillment record status MUST be PENDING

## Fund Management

The following methods are used to manage funds after fulfillment:

#### `beneficiaryWithdraw`

```solidity
/**
 * @notice Withdraws the beneficiary's available balance to release (fulfilled with success)
 * @param serviceID The service identifier
 * @param token The address of the ERC20 token
 */
function beneficiaryWithdraw(uint256 serviceID, address token) external
```

Withdraws the beneficiary's available token balance to release (fulfilled with success).

*Effects*:
* Validates the caller is the manager
* Retrieves the service details from the registry
* Transfers the releaseable pool amount to the beneficiary
* Resets the releaseable pool for the service and token

*Requirements*:
* Caller MUST be the manager
* Releaseable pool MUST have a non-zero balance

#### `withdrawAccumulatedFees`

```solidity
/**
 * @notice Withdraws the accumulated fees for a service
 * @param serviceID The service identifier
 * @param token The address of the ERC20 token
 */
function withdrawAccumulatedFees(uint256 serviceID, address token) external
```

Withdraws the accumulated token fees for a given service ID.

*Effects*:
* Validates the caller is the manager
* Retrieves the service details from the registry
* Transfers the accumulated fees to the beneficiary
* Resets the accumulated fees for the service and token
* Emits `ERC20FeesWithdrawn` event

*Requirements*:
* Caller MUST be the manager
* Accumulated fees MUST have a non-zero balance

## Stablecoin Swapping (V1_2)

The following methods are available in V1_2 for swapping ERC20 tokens to stablecoins:

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
 * - The fulfillment record must exist.
 * 
 * @param serviceId The service identifier.
 * @param recordId The fulfillment record identifier.
 * @param swapData The struct capturing the aggregator call data, tokens, and amounts.
 */
function swapPoolsToStable(
    uint256 serviceId,
    uint256 recordId,
    SwapData calldata swapData
) external nonReentrant onlyManager
```

Swaps both releaseable pool and accumulated fees to stablecoins in a single transaction using an off-chain generated DEX aggregator call.

*Effects*:
* Retrieves the fulfillment record
* Calls `SwapLib.swapERC20ToStable` to perform the swap

*Requirements*:
* Caller MUST be the manager
* Fulfillment record MUST exist

#### `subtractPoolsAndFees`

```solidity
/**
 * @dev Subtracts the pools and fees for a given service.
 * @dev Only the manager can call this.
 * @param serviceId The service identifier.
 * @param token The token address.
 * @param amount The amount to subtract.
 * @param fees The fees to subtract.
 */
function subtractPoolsAndFees(uint256 serviceId, address token, uint256 amount, uint256 fees) external onlyManager
```

Subtracts the releaseable pools and accumulated fees for a given service and token.

*Effects*:
* Subtracts the specified amount from the releaseable pool
* Subtracts the specified fees from the accumulated fees
* Emits `PoolsAndFeesSubtracted` event

*Requirements*:
* Caller MUST be the manager

#### `withdrawFulfillerPoolAndFees`

```solidity
/**
 * @dev withdraws an amount to a beneficiary
 * @dev Only the manager can call this.
 * @param token The token address
 * @param amount The amount to withdraw
 * @param fees The fees to withdraw
 * @param beneficiary The beneficiary address
 * @param feesBeneficiary The fees beneficiary address
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

### View Functions

```solidity
function getReleasePools(uint256 serviceId, address token) external view returns (uint256)
```
Gets the releaseable pool for a service and token.

```solidity
function getReleaseablePools(uint256 serviceId, address token) external view returns (uint256)
```
Gets the releaseable pools for a service and token (V1_2).

```solidity
function getERC20FeesFor(address token, uint256 serviceId) external view returns (uint256)
```
Gets the accumulated fees for a service and token.

```solidity
function getFulfillmentRecord(uint256 serviceId, uint256 recordId) external view returns (ERC20FulFillmentRecord memory)
```
Gets a fulfillment record by service ID and record ID.

## Events

```solidity
event FulfillmentEscrowed(uint256 indexed serviceId, uint256 indexed recordId, ERC20FulFillmentRequest request)
```
Emitted when tokens are escrowed for a fulfillment request.

```solidity
event FulfillmentRegistered(uint256 indexed serviceId, uint256 indexed recordId, FulFillmentResult result)
```
Emitted when a fulfillment result is registered.

```solidity
event BeneficiaryWithdrawn(uint256 indexed serviceId, address indexed beneficiary, address indexed token, uint256 amount)
```
Emitted when tokens are withdrawn to a beneficiary.

```solidity
event FeesWithdrawn(uint256 indexed serviceId, address indexed beneficiary, address indexed token, uint256 amount)
```
Emitted when fees are withdrawn.

## Errors

```solidity
error InvalidManager(address manager)
```
Emitted when the caller is not the authorized manager.

```solidity
error InvalidServiceId(uint256 serviceId)
```
Emitted when an invalid service ID is provided.

```solidity
error InsufficientAllowance(uint256 required, uint256 provided)
```
Emitted when insufficient token allowance is provided for escrow.

```solidity
error InvalidFulfillmentResult(FulFillmentResultState status)
```
Emitted when a fulfillment result has an invalid status.

## Access Control

The contract uses a manager-based access control pattern:

- `onlyManager` modifier restricts functions to be called only by the authorized manager (BandoFulfillmentManager)
- The manager is set during initialization and can be updated by the owner

## Integration Points

- Interacts with `IFulfillableRegistry` to validate services and get fee information
- Interacts with `IERC20` to transfer tokens for escrow and withdrawals
- Receives calls from `BandoFulfillmentManager` to register fulfillments and manage withdrawals
- In V1_2, interacts with DEX aggregators to swap tokens to stablecoins
