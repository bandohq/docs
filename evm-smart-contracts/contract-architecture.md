# Contract Architecture

## Component Interactions

### Entry Points

- **BandoRouter**: The main entry point for users to submit fulfillment requests
- **BandoFulfillmentManager**: The entry point for fulfillers to register fulfillment results

### Data Flow

1. **Request Submission**:
   - User submits a request through the BandoRouter
   - Router forwards the request to the appropriate fulfillable contract

2. **Fund Management**:
   - BandoFulfillable holds native currency in escrow
   - BandoERC20Fulfillable holds ERC20 tokens in escrow
   - Both contracts track fulfillment records and manage fund pools

3. **Fulfillment Registration**:
   - Fulfiller submits fulfillment results to the BandoFulfillmentManager
   - Manager validates the fulfiller's authorization with the FulfillableRegistry
   - Manager registers the result with the appropriate fulfillable contract

4. **Fund Release**:
   - Upon successful fulfillment, funds are released to beneficiaries
   - Fees are accumulated for fulfillers
   - In V1.2, funds can be swapped to stablecoins before release

## Proxy Architecture

Each core contract is deployed behind a proxy:

- **BandoRouterProxy**: Proxy for the BandoRouter
- **BandoFulfillmentManagerProxy**: Proxy for the BandoFulfillmentManager
- **BandoFulfillableProxy**: Proxy for the BandoFulfillable
- **BandoERC20FulfillableProxy**: Proxy for the BandoERC20Fulfillable
- **FulfillableRegistryProxy**: Proxy for the FulfillableRegistry
- **ERC20TokenRegistryProxy**: Proxy for the ERC20TokenRegistry
