---
description: How does the Bando Protocol defends to different kinds of threats.
---

# Security Considerations

This page is intended to document possible ways that an attacker could take advantage of the protocol's smart contracts, and what measures we take to mitigate the attacks.

### Reentrancy Attacks

Despite the use of ReentrancyGuardUpgradeable, there might still be reentrancy vulnerabilities if not properly implemented in all contracts, especially in functions like withdrawRefund and withdrawERC20Refund in the manager contract. For the router contract we must also make sure that the deposit functions are always guarded against reentrancy.

### Unauthorized Access

If not protected by access control, the manager and escrow functions could be executed by anyone. It is paramount to have a layer of access control for all functions on the manager, fulfillable escrow contracts and registries.

### Front-Running

Transactions like registering fulfillments or withdrawing refunds could be front-run by attackers monitoring the mempool.

### Denial of Service (DoS) Attacks

An attacker could potentially DoS the contract by manipulating gas costs or causing functions to revert consistently.

### Programming Errors

Incorrect implementation of business logic, especially in complex fulfillment scenarios, could lead to unexpected behaviors exploitable by attackers.

### Upgrade Vulnerabilities

As the contract is upgradeable (UUPSUpgradeable), flaws in the upgrade process or malicious upgrades could compromise the entire system.

### Cross-Contract Vulnerabilities

Interactions between multiple contracts in the suite could introduce vulnerabilities if not carefully designed and tested.

