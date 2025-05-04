## SwapLib

## Overview

The `SwapLib` library provides functionality for swapping tokens to stablecoins using DEX aggregators. It handles the logic for distributing swapped amounts proportionally between releaseable pools and fee pools. The library is used by the V1_2 versions of the fulfillable contracts to enable stablecoin swapping capabilities.

The library consists of two main components:
* `SwapLib`: For handling ERC20 token swaps to stablecoins
* `SwapNativeLib`: For handling native currency swaps to stablecoins

Both components share similar functionality but are optimized for their respective token types. The library is responsible for:
* Validating swap parameters and token addresses
* Performing the actual swap call to the DEX aggregator
* Calculating the received stablecoin amount
* Distributing the swapped stablecoins proportionally between releaseable pools and fee pools

The `SwapLib` responsibilities can be broken down into the following concepts:
* [Swap Data Structure](#swap-data-structure)
* [ERC20 Token Swapping](#erc20-token-swapping)
* [Native Currency Swapping](#native-currency-swapping)
* [Swap Execution](#swap-execution)
* [Distribution Calculation](#distribution-calculation)

## Swap Data Structure

#### `SwapData`

```solidity
/**
 * @dev Struct for swap data
 */
struct SwapData {
    address toToken;
    uint256 amount;
    address payable callTo;
    bytes callData;
}
```

The `SwapData` struct captures all the information needed to perform a swap:
* `toToken`: The destination stablecoin address
* `amount`: The amount of tokens to swap
* `callTo`: The DEX aggregator contract address
* `callData`: The encoded call data for the aggregator

## ERC20 Token Swapping

#### `swapERC20ToStable`

```solidity
/**
 * @dev Swaps ERC20 token pools to stablecoins in a single transaction
 * Requirements:
 * - The fromToken must have sufficient combined balance.
 * 
 * @param serviceId The service identifier.
 * @param swapData The struct capturing the aggregator call data, tokens, and amounts.
 */
function swapERC20ToStable(
    mapping(uint256 => mapping(address => uint256)) storage _releaseablePools,
    mapping(uint256 => mapping(address => uint256)) storage _accumulatedFees,
    uint256 serviceId,
    SwapData calldata swapData,
    ERC20FulFillmentRecord memory fulfillmentRecord
) internal
```

Swaps ERC20 token pools to stablecoins in a single transaction. The function validates the token addresses and swap amount, then performs the swap and distributes the received stablecoins proportionally between the releaseable and fees pools.

*Effects*:
* Validates the token addresses and swap amount
* Calculates the total available balance from releaseable pools and accumulated fees
* Subtracts the swap amount from the appropriate pools
* Calls `_callSwap` to perform the actual swap
* Distributes the received stablecoins proportionally using `_distributeStableAmounts`
* Updates the releaseable pools and accumulated fees with the distributed stablecoin amounts
* Emits `PoolsSwappedToStable` event

*Requirements*:
* `fulfillmentRecord.token` MUST NOT be the zero address
* `swapData.toToken` MUST NOT be the zero address
* `swapData.amount` MUST be greater than zero
* The total available balance MUST be greater than or equal to `swapData.amount`

## Native Currency Swapping

#### `swapNativeToStable`

```solidity
/**
 * @dev Swaps token pools to stablecoins in a single transaction
 * Requirements:
 * - The fromToken must have sufficient combined balance.
 * 
 * @param serviceId The service identifier.
 * @param swapData The struct capturing the aggregator call data, tokens, and amounts.
 */
function swapNativeToStable(
    mapping(uint256 => mapping(address => uint256)) storage _releaseablePools,
    mapping(uint256 => mapping(address => uint256)) storage _accumulatedFees,
    uint256 serviceId,
    SwapData calldata swapData,
    FulFillmentRecord memory fulfillmentRecord
) internal
```

Swaps native currency pools to stablecoins in a single transaction. The function is similar to `swapERC20ToStable` but is optimized for native currency.

## Swap Execution

#### `_callSwap`

```solidity
/**
 * @dev Performs the actual swap call to the DEX aggregator
 * @param aggregator The DEX aggregator contract address
 * @param token The token to swap
 * @param swapData The swap data
 * @return receivedStable The amount of stablecoins received
 */
function _callSwap(
    address aggregator,
    address token,
    SwapData calldata swapData
) internal returns (uint256 receivedStable)
```

Performs the actual swap call to the DEX aggregator and calculates the received stablecoin amount. The function approves the aggregator to spend the tokens, calls the aggregator with the provided call data, and calculates the received stablecoin amount by comparing the balance before and after the swap.

## Distribution Calculation

#### `_distributeStableAmounts`

```solidity
/**
 * @dev Distributes the stable amount proportionally between the releaseable and fees pools
 * @notice This function is used to distribute the stable amount proportionally between the releaseable and fees pools
 * @dev Calculation: (receivedStable * fromReleaseableAmount) / totalSwapAmount
 * @dev Calculation: receivedStable - releaseableShare
 * @dev eg: receivedStable = 2022, tokenAmount = 1000, totalSwapAmount = 1011
 * @dev releaseableShare = (2022 * 1000) / 1011 = 2000
 * @dev feesShare = 2022 - 2000 = 22
 * @param totalSwapAmount The total amount of the swap
 * @param fromReleaseableAmount The amount of the releaseable pool
 * @param receivedStable The amount of stable received from the swap
 * @return releaseableShare The amount of the releaseable pool to distribute
 * @return feesShare The amount of the fees pool to distribute
 */
function _distributeStableAmounts(
    uint256 totalSwapAmount,
    uint256 fromReleaseableAmount,
    uint256 receivedStable
) internal pure returns (uint256 releaseableShare, uint256 feesShare)
```

Distributes the stable amount proportionally between the releaseable and fees pools. The function calculates the releaseable share based on the proportion of the releaseable amount to the total swap amount, and the fees share as the remainder.

---
