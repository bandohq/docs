---
description: Documenting Actors, Roles, Permissions, and System Invariants
---

# Access Control

The Bando Fulfillment Protocol implements an access control system across multiple contracts to manage various operations within the protocol. This system ensures that only authorized entities can perform specific actions, maintaining the security and integrity of the protocol's functionality.

## Roles

The protocol defines several key roles with distinct permissions.

### Protocol Owner

Type: Account

MPC wallet. Is the owner of the escrow contracts, router, manager and registries.\
It is in charge of upgrading proxies, setting configuration, initializing contracts, pausing functions, and recovering funds.

### Fulfillment Manager

Type: Smart Contract&#x20;

Responsible for managing operations for refund withdrawals, and order fulfillments.

### Router

Type: Smart Contract

Handles deposit operations and service requests to the escrow contracts. This isolate the escrow contracts as stand alone to be able to enforce escrow rules strictly.

### Fulfiller

Type: Account

The actual vendor of a set of products of services. The protocol is designed to support one fulfiller per service. So a network of different fulfillers can be built.\
Performs fulfillment-related tasks for specific services. It is the entity responsible for managing the fulfillments to its own services. Normally this would be a hot wallet integration or more securely, a multi-sig operation signed by the fulfiller of a product or service.

### Beneficiary

Type: Account

Receives released funds for a service after fulfilling a request.

## Enforcing Access Control

Access control is enforced through various mechanisms:

* Ownership: Most contracts inherit from `OwnableUpgradeable`, which provides owner-specific functions.
* Function Modifiers:
  * `onlyOwner: Restricts access to the contract owner.`
  * `nonReentrant: Prevents reentrancy attacks.`
  * `onlyManager: Restricts access to the manager (in FulfillableRegistry).`
* Explicit Checks: Many functions include explicit checks to verify the caller's identity:  &#x20;

```solidity
require(_router == msg.sender, "Caller is not the router");
require(_manager == msg.sender, "Caller is not the manager");
```

* Role-based Functions: Certain functions are designed to be called only by specific roles:
  * Router: `depositERC20`, `requestERC20Service`, `requestService`
  * Manager: `withdrawERC20Refund`, `registerFulfillment`, `beneficiaryWithdraw`, `addService`, `addServiceRef`
  * Fulfiller: Can call certain functions in `BandoFulfillmentManagerV1`
* Initialization: The initialize function in upgradeable contracts sets up initial ownership and can only be called once due to the initializer modifier.

## System Invariants

{% hint style="warning" %}
The best way to check the invariants across the whole contract suite is to run\
`yarn hardhat coverage` on the repo to run all integration test cases.
{% endhint %}

Below we enlist the main higher level invariants:

* Only the owner can:
  * Authorize contract upgrades (\_authorizeUpgrade)
  * Set critical addresses (manager, router, registry, escrow)
  * Pause and unpause contracts (in BandoRouterV1)
  * Add or remove tokens from the whitelist (in ERC20TokenRegistry)
  * Update service details (beneficiary, fee amount, fulfiller) in FulfillableRegistry
* Only the router can:
  * Process ERC20 deposits and service requests
  * Only the manager can:
  * Process ERC20 refunds
  * Register fulfillments
  * Initiate beneficiary withdrawals
  * Add services and service references to the FulfillableRegistry
* The Manager Contract can:
  * must be called by a fulfiller to withdraw funds
  * must be called by a fulfiller to register a fulfillment result
  * must be called by the protocol owner to set a new service to the catalog
  * must be called by the fulfiller or the owner to set a new validated reference.
* Fulfillers must:
  * Register fulfillments and withdraw refunds for their associated services (through `BandoFulfillmentManagerV1)`
* Public functions that don't modify state (e.g., view functions) are generally accessible to anyone.
* Internal functions can only be called by other functions within the same contract, enforcing additional access control layers.
* The `FulfillableRegistry` maintains a mapping of fulfillers to their associated services, ensuring that only authorized fulfillers can perform actions for specific services. It also maintains a map of validated service references pointing to a fulfiller's real-life reference that will be used to fulfill the payment.
* The `ERC20TokenRegistry` maintains a whitelist of allowed tokens, which can only be modified by the owner.
