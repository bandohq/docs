# Upgradeability

## Overview

The EVM Fulfillment Protocol uses the Universal Upgradeable Proxy Standard (UUPS) pattern to enable contract upgradeability while preserving state and contract addresses. This document explains the upgradeability mechanism and considerations.

## UUPS Proxy Pattern

The protocol implements the UUPS pattern, which has the following characteristics:

- The upgrade logic is stored in the implementation contract rather than the proxy
- This reduces deployment costs and simplifies proxy contracts
- Upgrade authorization is handled by the implementation contract's access control

## Proxy Contracts

Each core contract in the protocol has a corresponding proxy:

- **BandoRouterProxy**: Forwards calls to the BandoRouter implementation
- **BandoFulfillmentManagerProxy**: Forwards calls to the BandoFulfillmentManager implementation
- **BandoFulfillableProxy**: Forwards calls to the BandoFulfillable implementation
- **BandoERC20FulfillableProxy**: Forwards calls to the BandoERC20Fulfillable implementation
- **FulfillableRegistryProxy**: Forwards calls to the FulfillableRegistry implementation
- **ERC20TokenRegistryProxy**: Forwards calls to the ERC20TokenRegistry implementation

## Upgrade Process

The upgrade process follows these steps:

1. Deploy a new implementation contract
2. Call the `upgradeToAndCall` function on the current implementation, passing the address of the new implementation and a callback function to initialize the new implementation. This is in the 5.x version of OpenZeppelin contracts.
3. The proxy will now delegate all calls to the new implementation

## Storage Considerations

When upgrading contracts, storage layout must be preserved to maintain compatibility:

- New versions must not modify existing storage slot usage
- New storage variables must be added at the end of the contract
- Inheritance order must be maintained

The protocol follows these rules by using inheritance for versioning:

```solidity
// Example of storage inheritance pattern
contract BandoFulfillmentManagerV1 {
    // Original storage variables
}

contract BandoFulfillmentManagerV1_1 is BandoFulfillmentManagerV1 {
    // New storage variables for V1.1
}

contract BandoFulfillmentManagerV1_2 is BandoFulfillmentManagerV1_1 {
    // New storage variables for V1.2
}
```

## Access Control

Upgrade authorization is controlled through the `onlyOwner` modifier:

- Only the contract owner can initiate upgrades
- The owner is set during initialization and can be transferred
- This ensures that upgrades are performed by authorized parties

## Initialization

Since constructors are not used in the proxy pattern, initialization is handled through `initialize` functions:

- Each implementation contract has an `initialize` function
- The function can only be called once
- It sets up the initial state, including ownership

## Security Considerations

The upgradeability pattern introduces specific security considerations:

- The contract owner has significant power and must be secured
- Implementation contracts should be thoroughly audited
- Storage collisions must be avoided during upgrades
- Function selectors must not conflict between versions
