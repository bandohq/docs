---
description: How does the Bando Protocol defends to different kinds of threats.
---

# Security Considerations

This page is intended to document possible ways that an attacker could take advantage of the protocol's smart contracts, and what measures we take to mitigate the attacks.

### Reentrancy Attacks

Despite the use of ReentrancyGuardUpgradeable, there might still be reentrancy vulnerabilities if not properly implemented in all contracts, especially in functions like withdrawRefund and withdrawERC20Refund in the manager contract. For the router contract we must also make sure that the deposit functions are always guarded against reentrancy.

Mitigations

* We use the **nonReentrant** modifier consistently on all functions that involve transfers or external calls.
* We try to follow the checks-effects-interactions pattern.
* We use OpenZeppelin's ReentrancyGuard for all contracts that handle funds

### Unauthorized Access

If not protected by access control, the manager and escrow functions could be executed by anyone. It is paramount to have a layer of access control for all functions on the manager, fulfillable escrow contracts and registries.

Mitigations

* We make sure all sensitive functions are protected with appropriate access control modifiers (e.g., `onlyOwner`, `onlyManager`).
* We have a robust role-based access control system.
* We aim to regularly audit and test access control mechanisms.

### Denial of Service (DoS) Attacks

An attacker could potentially DoS the contract by manipulating gas costs or causing functions to revert consistently.

Mitigation\
We use pull payment patterns instead of push for the refunds withdrawal.

### Upgrade Vulnerabilities

The upgrade mechanism (UUPS) could be exploited to replace contract logic with malicious code.

Mitigation

* We Implement a multi-sig requirement for upgrades.
* Thoroughly testing and audit new implementations before upgrading.

### Centralization Risks

Centralized control over critical functions could be a single point of failure.

Mitigation

* We implement multi-sig wallets for critical operations.
* We will gradually transition to a more decentralized governance model.

### Insufficient Input Validation

Malformed or malicious input data could lead to unexpected behavior.

#### Mitigation:

Thorough input validation for all public and external functions.

### &#x20;Token Approval Exploits

Unlimited token approvals could be exploited if a token contract is compromised.

Mitigation

We use specific approval amounts instead of unlimited approvals

### Cross-Contract Vulnerabilities

Interactions between multiple contracts could lead to unexpected states or vulnerabilities.

Mitigation

* We implement proper access control between contracts. Specially in the escrow contract that must function as standalone and be interacted just through other contracts.
* We always conduct integration testing and audits of the entire system.

