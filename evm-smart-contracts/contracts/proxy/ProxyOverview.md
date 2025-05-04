## Proxy Contracts Overview

## Overview

The EVM Fulfillment Protocol uses the Universal Upgradeable Proxy Standard (UUPS) pattern to enable contract upgradeability. This approach allows the protocol to evolve while preserving contract state and addresses.

In the UUPS pattern, the upgrade functionality is implemented in the implementation contract itself, rather than in the proxy. This reduces the proxy contract size and gas costs while maintaining the ability to upgrade the implementation.

The proxy architecture in the EVM Fulfillment Protocol can be broken down into the following concepts:
* [Proxy Implementation](#proxy-implementation)
* [Upgrade Mechanism](#upgrade-mechanism)
* [Initialization](#initialization)
* [Security Considerations](#security-considerations)

## Proxy Implementation

All proxy contracts in the EVM Fulfillment Protocol extend OpenZeppelin's `ERC1967Proxy`, which implements the ERC-1967 standard for proxy storage slots. This ensures compatibility with tools and standards in the Ethereum ecosystem.

```solidity
// Example from BandoFulfillableProxy.sol
contract BandoFulfillableProxy is ERC1967Proxy {
    constructor(address _logic, bytes memory _data)
      ERC1967Proxy(_logic, _data)
    {}
}
```

The proxy contracts are minimal by design, containing only the constructor that sets up the initial implementation address and initialization data. All other functionality is delegated to the implementation contract.

## Upgrade Mechanism

The implementation contracts inherit from OpenZeppelin's `UUPSUpgradeable` contract, which provides the upgrade functionality. The key components of this mechanism are:

1. **`upgradeTo` Function**: Allows upgrading to a new implementation address.

```solidity
function upgradeTo(address newImplementation) external onlyProxy onlyOwner {
    _authorizeUpgrade(newImplementation);
    _upgradeToAndCallUUPS(newImplementation, new bytes(0), false);
}
```

2. **`upgradeToAndCall` Function**: Allows upgrading to a new implementation and calling a function in the same transaction.

```solidity
function upgradeToAndCall(address newImplementation, bytes memory data) external payable onlyProxy onlyOwner {
    _authorizeUpgrade(newImplementation);
    _upgradeToAndCallUUPS(newImplementation, data, true);
}
```

3. **`_authorizeUpgrade` Function**: Provides access control for upgrades, typically restricted to the contract owner.

```solidity
function _authorizeUpgrade(address newImplementation) internal override onlyOwner {}
```

## Initialization

Since constructors are not used in upgradeable contracts (as they're only run once during deployment), the protocol uses initializer functions to set up the initial state:

```solidity
function initialize(address initialOwner) public initializer {
    __UUPSUpgradeable_init();
    __Ownable_init(initialOwner);
    // Contract-specific initialization
}
```

The `initializer` modifier ensures that the initialization function can only be called once, preventing re-initialization attacks.

## Versioning Strategy

The EVM Fulfillment Protocol uses a versioned inheritance pattern for its implementation contracts:

```
BandoFulfillableV1
        ↑
BandoFulfillableV1_1
        ↑
BandoFulfillableV1_2
```

This approach allows for:

1. **Incremental Upgrades**: New versions can build upon previous versions, inheriting their functionality.
2. **Clear Version History**: The inheritance chain provides a clear history of contract versions.
3. **Backward Compatibility**: New versions maintain the same interface as previous versions, ensuring compatibility.

## Deployment Process

The deployment process for a proxy and its implementation follows these steps:

1. **Deploy Implementation**: Deploy the implementation contract without initialization.
2. **Prepare Initialization Data**: Encode the initialization function call with its parameters.
3. **Deploy Proxy**: Deploy the proxy contract, passing the implementation address and initialization data.
4. **Verify Deployment**: Confirm that the proxy is correctly delegating to the implementation.

## Upgrade Process

To upgrade a contract in the EVM Fulfillment Protocol:

1. **Deploy New Implementation**: Deploy the new implementation contract.
2. **Call `upgradeTo`**: Call the `upgradeTo` function on the current implementation through the proxy, passing the new implementation address.
3. **Verification**: Verify that the proxy is now delegating to the new implementation.

## Security Considerations

### Access Control

All upgrade functions in the protocol are protected by the `onlyOwner` modifier, ensuring that only the contract owner can perform upgrades.

### Storage Layout

When upgrading implementation contracts, it's critical to maintain the same storage layout to prevent data corruption. The protocol follows these practices:

1. **Append-Only Storage**: New storage variables are only added at the end of the contract.
2. **No Storage Removal**: Existing storage variables are never removed, only deprecated.
3. **Type Preservation**: The types of storage variables are never changed.

### Implementation Validation

Before upgrading to a new implementation, the protocol ensures that:

1. The new implementation is compatible with the existing storage layout.
2. The new implementation has been thoroughly tested.
3. The new implementation has undergone security audits when appropriate.

### Initialization Protection

All initialization functions use the `initializer` modifier to prevent re-initialization attacks.

---
