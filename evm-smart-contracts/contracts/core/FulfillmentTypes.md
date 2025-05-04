## FulfillmentTypes

| File | Notes |
| -------- | -------- |
| [`FulfillmentTypes.sol`](../../contracts/FulfillmentTypes.sol) | Core data structures for the EVM Fulfillment Protocol |

## Overview

The `FulfillmentTypes.sol` file defines the core data structures used throughout the EVM Fulfillment Protocol. These types represent the fundamental entities in the fulfillment process, including requests, results, and records for both native currency and ERC20 token fulfillments.

The defined types are used across all components of the protocol:
* `FulFillmentRequest` and `ERC20FulFillmentRequest` are submitted by users through the `BandoRouter`
* `FulFillmentResult` is submitted by fulfillers through the `BandoFulfillmentManager`
* `FulFillmentRecord` and `ERC20FulFillmentRecord` are stored in the `BandoFulfillable` and `BandoERC20Fulfillable` contracts
* `FulFillmentResultState` is used to track the status of fulfillments throughout the protocol

The types in this file can be categorized as follows:
* [Fulfillment States](#fulfillment-states)
* [Native Currency Fulfillment Types](#native-currency-fulfillment-types)
* [ERC20 Token Fulfillment Types](#erc20-token-fulfillment-types)

## Fulfillment States

#### `FulFillmentResultState`

```solidity
/**
 * @notice Enum with states for fulfillment results
 */
enum FulFillmentResultState {
    FAILED,
    SUCCESS,
    PENDING,
    REFUNDED
}
```

Represents the possible states of a fulfillment:
* `FAILED`: The fulfillment attempt failed and cannot be completed
* `SUCCESS`: The fulfillment was successfully completed
* `PENDING`: The fulfillment is in progress and awaiting completion
* `REFUNDED`: The funds were refunded to the requester

## Native Currency Fulfillment Types

#### `FulFillmentRequest`

```solidity
/**
 * @notice Anybody can submit a fulfillment request through a router
 * @dev This struct represents a fulfillment request
 */
struct FulFillmentRequest {
    /// @notice Address of the payer
    address payer;
    /// @notice Amount in wei
    uint256 weiAmount;
    /// @notice Fiat amount to be charged for the fulfillable
    uint256 fiatAmount;
    /// @notice Identifier required to route the payment to the user's destination
    string serviceRef;
}
```

Represents a native currency fulfillment request that is submitted by a user through the `BandoRouter`.

#### `FulFillmentResult`

```solidity
/**
 * @notice A fulfiller will submit a fulfillment result in this format
 * @dev This struct represents the result of a fulfillment
 */
struct FulFillmentResult {
    /// @notice ID of the fulfillment record
    uint256 id;
    /// @notice ID coming from the fulfiller as proof
    string externalID;
    /// @notice The fulfillment external receipt URI
    string receiptURI;
    /// @notice Status of the fulfillment
    FulFillmentResultState status;   
}
```

Represents the result of a fulfillment that is submitted by a fulfiller through the `BandoFulfillmentManager`.

#### `FulFillmentRecord`

```solidity
/**
 * @notice The fulfiller will accept FulfillmentResults submitted to it,
 * and if valid, will persist them on-chain as FulfillmentRecords
 * @dev This struct represents a record of a fulfillment
 */
struct FulFillmentRecord {
    /// @notice Auto-incremental ID generated in contract
    uint256 id;
    /// @notice Identifier required to route the payment to the user's destination
    string serviceRef;
    /// @notice Address of the fulfiller
    address fulfiller;
    /// @notice ID coming from the fulfiller as proof
    string externalID;
    /// @notice Address of the payer
    address payer;
    /// @notice Amount in wei
    uint256 weiAmount;
    /// @notice Fee amount charged in wei
    uint256 feeAmount;
    /// @notice Fiat amount to be charged for the fulfillable
    uint256 fiatAmount;
    /// @notice Time at which the fulfillment was submitted
    uint256 entryTime;
    /// @notice The fulfillment external receipt URI
    string receiptURI;
    /// @notice Status of the fulfillment
    FulFillmentResultState status;
}
```

Represents a record of a native currency fulfillment that is stored in the `BandoFulfillable` contract.

## ERC20 Token Fulfillment Types

#### `ERC20FulFillmentRequest`

```solidity
/**
 * @notice Anybody can submit a fulfillment request through a router
 * @dev This struct represents an ERC20 fulfillment request
 */
struct ERC20FulFillmentRequest {
    /// @notice Address of the payer
    address payer;
    /// @notice Fiat amount to be charged for the fulfillable
    uint256 fiatAmount;
    /// @notice Identifier required to route the payment to the user's destination
    string serviceRef;
    /// @notice Address of the ERC20 token to be used for the payment
    address token;
    /// @notice Amount of tokens
    uint256 tokenAmount;
}
```

Represents an ERC20 token fulfillment request that is submitted by a user through the `BandoRouter`.

#### `ERC20FulFillmentRecord`

```solidity
/**
 * @notice The fulfiller will accept FulfillmentResults submitted to it,
 * and if valid, will persist them on-chain as FulfillmentRecords
 * @dev This struct represents a record of an ERC20 fulfillment
 */
struct ERC20FulFillmentRecord {
    /// @notice Auto-incremental ID generated in contract
    uint256 id;
    /// @notice Identifier required to route the payment to the user's destination
    string serviceRef;
    /// @notice Address of the fulfiller
    address fulfiller;
    /// @notice Address of the ERC20 token to be used for the payment
    address token;
    /// @notice ID coming from the fulfiller as proof
    string externalID;
    /// @notice Address of the payer
    address payer;
    /// @notice Amount of tokens
    uint256 tokenAmount;
    /// @notice Fee amount charged in tokens
    uint256 feeAmount;
    /// @notice Fiat amount to be charged for the fulfillable
    uint256 fiatAmount;
    /// @notice Time at which the fulfillment was submitted
    uint256 entryTime;
    /// @notice The fulfillment external receipt URI
    string receiptURI;
    /// @notice Status of the fulfillment
    FulFillmentResultState status;
}
```

Represents a record of an ERC20 token fulfillment that is stored in the `BandoERC20Fulfillable` contract.

---
