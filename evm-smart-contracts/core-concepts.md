# Core Concepts

## Introduction

The EVM Fulfillment Protocol provides a robust infrastructure for managing fulfillment services on EVM-compatible blockchains. This document explains the core concepts that form the foundation of the protocol.

## Key Components

### Services

A service in the protocol represents a fulfillment offering that can be requested by users. Each service has:

- **Service ID**: A unique identifier for the service
- **Beneficiary**: The address that receives the released funds after successful fulfillment
- **Fulfiller**: The address authorized to process fulfillment requests for the service

Services are registered in the FulfillableRegistry contract and can have multiple service references to identify different variations or instances of the service.

### Fulfillment Process

The fulfillment process follows these steps:

1. **Service Registration**: A service is registered in the FulfillableRegistry
2. **Fulfillment Request**: A user submits a fulfillment request through the BandoRouter
3. **Escrow**: Funds are held in escrow in either BandoFulfillable (native currency) or BandoERC20Fulfillable (ERC20 tokens)
4. **Fulfillment Processing**: The authorized fulfiller processes the request and submits a FulfillmentResult
5. **Result Registration**: The result is registered in the BandoFulfillmentManager
6. **Fund Release**: Upon successful fulfillment, funds are released to the beneficiary minus fees

### Fulfillment States

Fulfillment requests can have the following states:

- **PENDING**: Initial state when a request is submitted
- **SUCCESS**: The fulfillment was successfully completed
- **FAILED**: The fulfillment failed to complete
- **REFUNDED**: The funds were refunded to the requester

### Fee Model

The protocol includes a fee mechanism:

- Fees are specified in basis points (1/100 of a percent)
- Each service can have its own fee configuration
- Fees are collected upon successful fulfillment
- Accumulated fees can be withdrawn by the fulfiller

### Stablecoin Swapping

Version 1.2 of the protocol introduces the ability to swap funds to stablecoins:

- Uses external DEX aggregators for optimal swap rates
- Supports swapping both releaseable pools and accumulated fees
- Distributes swapped stablecoins proportionally between pools
- Requires whitelisted aggregator addresses for security
