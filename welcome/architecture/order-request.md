---
description: How the Bando Fulfillment Protocol receives new requests.
---

# Order Request

## How Does Requesting an Order Works?

Right after a user has selected a product or service to buy, the wallet will send a request to the Bando Fulfillment Protocol to create a new order. Before submitting a request, the according ref MUST have been verified by the Bando backend system and ingested to the RefRegistry smart contract. Once that is in place, a user SHALL interact with the BandoRouter contract to submit a fulfillment request.

The Bando Router Contract is a smart contract responsible for routing the user's service request to the appropriate Bando Escrow contract using a managed Service Registry contract. The process follows these steps:

1. The BandoRouter first checks the RefRegistry to validate the payment reference
2. If the reference is valid, the router looks up the appropriate Escrow contract using the Service Registry
3. Once the router verifies the reference and identifies the correct Escrow, it transfers the funds to the escrow
4. The router contract then emits a ServiceRequested event with the necessary data
5. Once the funds for the request are in the corresponding Escrow contract, the fulfiller is notified and becomes responsible for processing the product or service request and fulfilling the user's request off-chain

```mermaid
sequenceDiagram
    actor User
    box cyan smart contracts
	    participant BandoRouter
	    participant RefRegistry
	    participant ServiceRegistry
	    participant BandoEscrow
    end
    participant Fulfiller
    User->>BandoRouter: Submit fulfillment request
    BandoRouter->>RefRegistry: Validate payment reference
    RefRegistry-->>BandoRouter: Confirmation of valid reference
    BandoRouter->>ServiceRegistry: Look up appropriate Escrow contract
    ServiceRegistry-->>BandoRouter: Return Escrow contract details
    BandoRouter->>BandoEscrow: Transfer funds
    BandoRouter->>BandoRouter: Emit ServiceRequested event
    Fulfiller->>BandoEscrow: Monitor for funds
    Fulfiller->>Fulfiller: Process request off-chain
```

## Flow of Funds for a Service Request

### Initial Request and Escrow

**Token Approval**

The user (payer) must first approve the BandoRouter contract to spend their ERC20 tokens by calling `approve()` on the ERC20 token contract.

#### Service Request

The user calls `requestERC20Service()` on the `BandoRouterV1_1` contract, specifying:

* Service ID
* ERC20 token address
* Token amount
* Service reference
* Other request parameters

**Request Validation**

The router validates the request using `FulfillmentRequestLib.validateERC20Request()` to ensure:

* The service exists in the registry
* The service reference is valid
* The token is supported

**Fee Calculation**

The router calculates service fees using `FulfillmentRequestLib.calculateFees()`.

**Token Transfer**

The router transfers tokens from the user to the escrow contract using `safeTransferFrom().`

**Escrow Deposit**

The router calls `depositERC20()` on the `BandoERC20Fulfillable` contract.

Record Creation:&#x20;

The escrow contract creates a fulfillment record and stores deposit information.

<figure><img src="../../.gitbook/assets/mermaid-diagram-2025-03-14-175748.png" alt=""><figcaption></figcaption></figure>
