## ERC20TokenRegistry

| File | Notes |
| -------- | -------- |
| [`ERC20TokenRegistryV1.sol`](../../contracts/periphery/registry/ERC20TokenRegistryV1.sol) | Implementation of the ERC20 token registry |
| [`IERC20TokenRegistry.sol`](../../contracts/periphery/registry/IERC20TokenRegistry.sol) | Interface for the ERC20 token registry |

## Overview

The `ERC20TokenRegistry` contract manages a whitelist of ERC20 tokens that can be used for fulfillment services in the Bando Protocol. It ensures that only approved tokens can be used for ERC20 fulfillment requests and manages swap fee basis points for each token.

The `ERC20TokenRegistry` is responsible for:
* Maintaining a whitelist of approved ERC20 tokens
* Managing swap fee basis points for each whitelisted token
* Providing validation functions to check if a token is whitelisted

The contract uses the UUPS (Universal Upgradeable Proxy Standard) pattern for upgradeability and inherits from OpenZeppelin's OwnableUpgradeable for access control.

The `ERC20TokenRegistry` responsibilities can be broken down into the following concepts:
* [Token Whitelisting](#token-whitelisting)
* [Swap Fee Management](#swap-fee-management)
* [Token Validation](#token-validation)

## Token Whitelisting

The following methods are used to manage the whitelist of ERC20 tokens:

#### `addToken`

```solidity
/**
 * @notice Adds a token to the whitelist
 * @dev Only the contract owner can add tokens
 * @param token The address of the token to add
 * @param swapFeeBasisPoints The swap fee percentage for the token
 */
function addToken(address token, uint16 swapFeeBasisPoints) public onlyOwner
```

Adds a token to the whitelist with the specified swap fee basis points. Only the contract owner can add tokens.

*Effects*:
* Checks if the token is already whitelisted
* Adds the token to the whitelist
* Sets the swap fee basis points for the token
* Emits `TokenAdded(token, swapFeeBasisPoints)` event

*Requirements*:
* Caller MUST be the contract owner
* Token MUST NOT already be whitelisted

#### `removeToken`

```solidity
/**
 * @notice Removes a token from the whitelist
 * @dev Only the contract owner can remove tokens
 * @param token The address of the token to remove
 */
function removeToken(address token) public onlyOwner
```

Removes a token from the whitelist. Only the contract owner can remove tokens.

*Effects*:
* Checks if the token is whitelisted
* Removes the token from the whitelist
* Emits `TokenRemoved(token)` event

*Requirements*:
* Caller MUST be the contract owner
* Token MUST be whitelisted

## Swap Fee Management

The following method is used to manage swap fee basis points for whitelisted tokens:

#### `updateSwapFeeBasisPoints`

```solidity
/**
 * @notice Updates the swap fee percentage for a token
 * @dev Only the contract owner can update the swap fee percentage
 * @param token The address of the token to update
 * @param swapFeeBasisPoints The new swap fee percentage
 */
function updateSwapFeeBasisPoints(address token, uint16 swapFeeBasisPoints) public onlyOwner
```

Updates the swap fee basis points for a token. Only the contract owner can update swap fee basis points.

*Effects*:
* Validates the swap fee basis points
* Updates the swap fee basis points for the token
* Emits `SwapFeeBasisPointsUpdated(token, swapFeeBasisPoints)` event

*Requirements*:
* Caller MUST be the contract owner
* `swapFeeBasisPoints` MUST be between 0 and 10000

## Token Validation

The following method is used to check if a token is whitelisted:

#### `isTokenWhitelisted`

```solidity
/**
 * @notice Checks if a token is whitelisted
 * @param token The address of the token to check
 * @return bool True if the token is whitelisted, false otherwise
 */
function isTokenWhitelisted(address token) public view returns (bool)
```

Checks if a token is whitelisted. Returns true if the token is whitelisted, false otherwise.

## Events

```solidity
event TokenAdded(address indexed token, uint16 swapFeeBasisPoints)
```
Emitted when a token is added to the whitelist.

```solidity
event TokenRemoved(address indexed token)
```
Emitted when a token is removed from the whitelist.

```solidity
event SwapFeeBasisPointsUpdated(address indexed token, uint16 swapFeeBasisPoints)
```
Emitted when the swap fee basis points for a token are updated.

## Errors

```solidity
error TokenNotWhitelisted(address token)
```
Emitted when attempting to perform an operation on a token that is not whitelisted.

```solidity
error TokenAlreadyWhitelisted(address token)
```
Emitted when attempting to add a token that is already whitelisted.

```solidity
error InvalidSwapFeeBasisPoints(uint16 swapFeeBasisPoints)
```
Emitted when attempting to set invalid swap fee basis points.

## Parameterization

* `whitelist`: Mapping of token addresses to their whitelist status
* `_swapFeeBasisPoints`: Mapping of token addresses to their swap fee basis points

---
