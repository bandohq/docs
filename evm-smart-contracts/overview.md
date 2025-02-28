---
description: Bando Fulfillment Protocol smart contracts for the Ethereum Virtual Machine.
---

# EVM Smart Contracts | Integration Guide | Bando

This smart contracts suite includes various contracts that manage services, fulfillments, and interactions with ERC20 tokens. The primary contract in this suite is the `Bando[ERC20]FulfillableV1`, which serves as the main escrow for the fulfillment process.

### Upgradeability

User-facing contracts are deployed using the UUPS (ERC1967) Standard for proxy upgradeability.

### Pausability

Only the Bando Router contract is pausable to shut down the deposits to the escrow contract on an incident situation.

### Bando Fulfillment Manager

- **File:** `BandoFulfillmentManagerV1.sol`\
  This contract manages services and fulfillables for the Bando protocol. It inherits from `OwnableUpgradeable`, `UUPSUpgradeable`, and `ReentrancyGuardUpgradeable` to provide access control, upgradeability, and protection against reentrancy attacks.
- **Features:**
  - **Service Management:** Allows the owner to set service registry, escrow addresses, and register services.
  - **Fulfillment Registration:** Enables fulfillers to register fulfillment results and withdraw refunds.
  - **Event Emission:** Emits events for service additions to track changes in the contract state.
  - Refund Authorization: The Manager contract authorizes a refund on behalf of a fulfiller.

### Bando Router

- File: `BandoRouterV1.sol`\
  This contract is supposed to be user-facing. It manages the deposit functions that route the payment to the appropriate service/token/payer relation. It inherits from `OwnableUpgradeable`, `UUPSUpgradeable`, and `ReentrancyGuardUpgradeable` to provide access control, upgradeability, and protection against reentrancy attacks.
- Features:
  - Validate payment ref: Use the service registry to validate the request payment ref.
  - Handles deposits of native currency to the escrow.
  - Handles support for ERC20 deposits to the escrow.

The router emits the following events:

```
    emit ServiceRequested(serviceID, request);
```

```
    emit ERC20ServiceRequested(serviceID, request);
```

Both events are emitted after the transfer has been issued to the escrow contracts.

### Bando Fulfillable Registry

- **File:** `FulfillableRegistry.sol`\
  A registry contract in charge of storing relational mappings of the product catalog and related info supported by the fulfillers.
- Features:
  - Stores the catalog data related to ids.
  - Stores payment ref ids validated off chain.

### Bando ERC20 Fulfillable

- **File:** `BandoERC20FulfillableV1.sol`\
  An escrow contract that manages ERC20 token fulfillments, allowing for the deposit, registration and withdrawal of ERC20 tokens. It can only be called by the router or the manager.
- Features:
  - Deposit a token amount from the bando router and relate it to the token, service and payer.
  - Withdraw an authorized refund from the manager contract.
  - Register a completed order fulfillment to release the funds.
  - Release funds to a configured beneficiary.

### Bando Fulfillable

- **File:** `BandoFulfillableV1.sol`\
  A contract that implements the fulfillable logic for the BFP, allowing for the execution of fulfillment actions. It can only be called by the router or the manager.
- Features:
  - Deposit an amount from the bando router and relate it to the service and payer.
  - Withdraw an authorized refund from the manager contract.
  - Register a completed order fulfillment to release the funds.
  - Release funds to a configured beneficiary.
