## BandoFulfillmentManager

| File | Notes |
| -------- | -------- |
| [`BandoFulfillmentManagerV1.sol`](../../contracts/BandoFulfillmentManagerV1.sol) | Initial version with core functionality |
| [`BandoFulfillmentManagerV1_1.sol`](../../contracts/BandoFulfillmentManagerV1_1.sol) | Enhanced version with optimizations |
| [`BandoFulfillmentManagerV1_2.sol`](../../contracts/BandoFulfillmentManagerV1_2.sol) | Latest version with stablecoin swapping |
| [`IBandoFulfillable.sol`](../../contracts/IBandoFulfillable.sol) | Interface for native currency fulfillable |
| [`IBandoERC20Fulfillable.sol`](../../contracts/IBandoERC20Fulfillable.sol) | Interface for ERC20 token fulfillable |

## Overview

The `BandoFulfillmentManager` is the central component of the EVM Fulfillment Protocol that manages the registration of fulfillment results and coordinates between fulfillable contracts and the service registry. It serves as the primary entry point for fulfillers to register the results of fulfillment operations and manages the withdrawal of funds for both native currency and ERC20 tokens.

The `BandoFulfillmentManager` is responsible for:
* Validating that only authorized fulfillers can register results for their services
* Registering fulfillment results with the appropriate fulfillable contracts
* Managing withdrawals of released funds to beneficiaries
* Managing withdrawals of accumulated fees to fulfillers
* In V1_2, managing DEX aggregator whitelisting and token swapping to stablecoins

The contract follows a versioned inheritance pattern:
* `BandoFulfillmentManagerV1`: Base functionality for fulfillment registration
* `BandoFulfillmentManagerV1_1`: Enhanced functionality and optimizations
* `BandoFulfillmentManagerV1_2`: Added support for stablecoin swapping via DEX aggregators

The `BandoFulfillmentManager` responsibilities can be broken down into the following concepts:
* [Fulfillment Registration](#fulfillment-registration)
* [Fund Withdrawal](#fund-withdrawal)
* [Stablecoin Swapping](#stablecoin-swapping)
* [DEX Aggregator Management](#dex-aggregator-management)

## Fulfillment Registration

The following methods are called by fulfillers to register the results of fulfillment operations:

#### `registerFulfillment`

```solidity
/**
 * @dev Registers a fulfillment result for a native currency service.
 * @param serviceID The service identifier.
 * @param result The FulFillmentResult
 */
function registerFulfillment(uint256 serviceID, FulFillmentResult memory result) public virtual
```

Registers a fulfillment result for a native currency service. The function validates that the caller is the authorized fulfiller for the service and then registers the result with the native currency fulfillable contract.

*Effects*:
* Validates the caller is the authorized fulfiller for the service
* Calls `IBandoFulfillable(_escrow).registerFulfillment(serviceID, result)` to register the result
* Emits `FulfillmentRegistered(serviceID, result)` event

*Requirements*:
* Caller MUST be the authorized fulfiller for the service or the contract owner

#### `registerERC20Fulfillment`

```solidity
/**
 * @dev Registers a fulfillment result for an ERC20 token service.
 * @param serviceID The service identifier.
 * @param result The FulFillmentResult
 */
function registerERC20Fulfillment(uint256 serviceID, FulFillmentResult memory result) public virtual
```

Registers a fulfillment result for an ERC20 token service. The function validates that the caller is the authorized fulfiller for the service and then registers the result with the ERC20 token fulfillable contract.

*Effects*:
* Validates the caller is the authorized fulfiller for the service
* Calls `IBandoERC20Fulfillable(_erc20_escrow).registerFulfillment(serviceID, result)` to register the result
* Emits `ERC20FulfillmentRegistered(serviceID, result)` event

*Requirements*:
* Caller MUST be the authorized fulfiller for the service or the contract owner

## Fund Withdrawal

The following methods are called by fulfillers to withdraw funds for their services:

#### `beneficiaryWithdraw`

```solidity
/**
 * @dev Withdraws the beneficiary's available balance to release (fulfilled with success).
 * Only the fulfiller can withdraw the releaseable pool.
 * @param serviceId The service identifier.
 */
function beneficiaryWithdraw(uint256 serviceId) public virtual
```

Withdraws the beneficiary's available native currency balance to release (fulfilled with success). Only the fulfiller can withdraw the releaseable pool.

*Effects*:
* Validates the caller is the authorized fulfiller for the service
* Calls `IBandoFulfillable(_escrow).beneficiaryWithdraw(serviceId)` to withdraw funds
* Emits `WithdrawnToBeneficiary(serviceId, service.beneficiary)` event

*Requirements*:
* Caller MUST be the authorized fulfiller for the service

#### `beneficiaryWithdrawERC20`

```solidity
/**
 * @dev Withdraws the beneficiary's available ERC20 token balance to release (fulfilled with success).
 * Only the fulfiller can withdraw the releaseable pool.
 * @param serviceId The service identifier.
 * @param token The token address.
 */
function beneficiaryWithdrawERC20(uint256 serviceId, address token) public virtual
```

Withdraws the beneficiary's available ERC20 token balance to release (fulfilled with success). Only the fulfiller can withdraw the releaseable pool.

*Effects*:
* Validates the caller is the authorized fulfiller for the service
* Calls `IBandoERC20Fulfillable(_erc20_escrow).beneficiaryWithdraw(serviceId, token)` to withdraw tokens
* Emits `WithdrawnToBeneficiary(serviceId, service.beneficiary)` event

*Requirements*:
* Caller MUST be the authorized fulfiller for the service
* `token` MUST be a valid token address

## Stablecoin Swapping

The following methods are available in V1_2 for swapping funds to stablecoins:

#### `fulfillERC20AndSwap`

```solidity
/**
 * @dev Registers a fulfillment result and swaps
 * both releaseable pool and accumulated fees to stablecoins in a single transaction.
 * The swap is done using an off-chain generated Dex aggregator call.
 * @param serviceID The service identifier.
 * @param result The FulFillmentResult
 * @param swapData The struct capturing the aggregator call data, tokens, and amounts.
 * @param swap Whether to perform the swap or not.
 */
function fulfillERC20AndSwap(
    uint256 serviceID,
    FulFillmentResult memory result,
    SwapData memory swapData,
    bool swap
) public virtual
```

Registers a fulfillment result and optionally swaps both releaseable pool and accumulated fees to stablecoins in a single transaction for ERC20 tokens. The swap is done using an off-chain generated DEX aggregator call.

*Effects*:
* Validates the caller is the authorized fulfiller for the service
* Validates the result status is SUCCESS
* Validates the aggregator is whitelisted
* Calls `IBandoERC20Fulfillable(_erc20_escrow).registerFulfillment(serviceID, result)` to register the result
* If `swap` is true, calls `IBandoERC20FulfillableV1_2(_erc20_escrow).swapPoolsToStable(serviceID, result.id, swapData)` to swap tokens
* Calls `_accumulateFulfillerReleaseablePoolAndFees(serviceID, swapData.toToken)` to accumulate funds
* Emits `ERC20FulfillmentRegistered(serviceID, result)` event

*Requirements*:
* Caller MUST be the authorized fulfiller for the service or the contract owner
* `result.status` MUST be `FulFillmentResultState.SUCCESS`
* `swapData.callTo` MUST be a whitelisted aggregator

## DEX Aggregator Management

The following methods are available in V1_2 for managing DEX aggregators:

#### `addAggregator`

```solidity
/**
 * @dev Adds a Dex aggregator address to the whitelist
 * @param aggregator The Dex aggregator contract address
 */
function addAggregator(address aggregator) external onlyOwner
```

Adds a DEX aggregator address to the whitelist. Only the contract owner can add aggregators.

*Effects*:
* Validates the aggregator address is not zero
* Adds the aggregator to the whitelist
* Emits `AggregatorAdded(aggregator)` event

*Requirements*:
* Caller MUST be the contract owner
* `aggregator` MUST NOT be the zero address

## Parameterization

* `_escrow`: Address of the native currency fulfillable contract
* `_erc20_escrow`: Address of the ERC20 token fulfillable contract
* `_serviceRegistry`: Address of the service registry contract

---
