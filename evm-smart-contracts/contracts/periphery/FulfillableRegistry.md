# FulfillableRegistry

## Overview

The FulfillableRegistry contract manages the registration and validation of fulfillment services. It stores service information, including fulfiller and beneficiary addresses, and handles service references and fee configurations.

## Inheritance

```
FulfillableRegistryV1
        ↑
FulfillableRegistryV1_1
        ↑
FulfillableRegistryV1_2
```

## Versions

### V1

The initial version provides core functionality for service registration and validation.

### V1_1

Adds enhanced management capabilities and optimizations.

### V1_2

Adds the ability to create services lazily when adding service references.

## Key Functions

### Service Management

```solidity
function addService(uint256 serviceId, Service memory service, uint16 fulfillmentFeeBasisPoints) external returns (bool)
```
Adds a new service to the registry.

```solidity
function removeServiceAddress(uint256 serviceId) external
```
Removes a service from the registry.

```solidity
function updateServiceBeneficiary(uint256 serviceId, address payable newBeneficiary) external
```
Updates the beneficiary of a service.

```solidity
function updateServicefeeAmountBasisPoints(uint256 serviceId, uint16 newfeeAmountBasisPoints) external
```
Updates the fee amount percentage of a service.

```solidity
function updateServiceFulfiller(uint256 serviceId, address newFulfiller) external
```
Updates the fulfiller of a service.

```solidity
/**
 * @notice Updates the beneficiary of a service
 * @param serviceId The service identifier
 * @param newBeneficiary The new beneficiary address
 */
function updateServiceBeneficiary(uint256 serviceId, address payable newBeneficiary) external onlyOwner
```

Updates the beneficiary address for a service.

*Effects*:
* Updates the beneficiary address in the service record
* Emits `ServiceBeneficiaryUpdated` event

*Requirements*:
* Caller MUST be the owner
* Service MUST exist

#### `updateServiceFulfiller`

```solidity
/**
 * @notice Updates the fulfiller of a service
 * @param serviceId The service identifier
 * @param newFulfiller The new fulfiller address
 */
function updateServiceFulfiller(uint256 serviceId, address newFulfiller) external onlyOwner
```

Updates the fulfiller address for a service.

*Effects*:
* Updates the fulfiller address in the service record
* Emits `ServiceFulfillerUpdated` event

*Requirements*:
* Caller MUST be the owner
* Service MUST exist
* New fulfiller address MUST NOT be zero

#### `getService`

```solidity
/**
 * @notice Retrieves the service details by its identifier
 * @param serviceId The service identifier
 * @return The service details and fulfillment fee basis points
 */
function getService(uint256 serviceId) external view returns (Service memory, uint16)
```

Retrieves the service details and its associated fulfillment fee basis points.

## Fulfiller Management

The following methods are used to manage fulfillers:

#### `addFulfiller`

```solidity
/**
 * @notice Registers a new fulfiller for a service
 * @param fulfiller The address of the fulfiller
 * @param serviceId The service identifier
 */
function addFulfiller(address fulfiller, uint256 serviceId) external onlyOwner
```

Registers a new fulfiller for a service.

*Effects*:
* Associates the fulfiller with the service
* Increments the fulfiller's service count
* Emits `FulfillerAdded` event

*Requirements*:
* Caller MUST be the owner
* Fulfiller address MUST NOT be zero

## Service References

The following methods are used to manage service references:

#### `addServiceRef`

```solidity
/**
 * @notice Adds a reference to a service
 * @param serviceId The service identifier
 * @param ref The reference to the service
 */
function addServiceRef(uint256 serviceId, string memory ref) external onlyOwner
```

Adds a reference to a service. References can be used for additional validation or metadata.

*Effects*:
* Stores the reference for the service
* Increments the reference count for the service
* Emits `ServiceRefAdded` event

*Requirements*:
* Caller MUST be the owner
* Service MUST exist

#### `isRefValid`

```solidity
/**
 * @notice Checks if a service reference is valid
 * @param serviceId The service identifier
 * @param ref The reference to check
 * @return Returns true if the reference is valid
 */
function isRefValid(uint256 serviceId, string memory ref) external view returns (bool)
```

Checks if a reference is valid for a given service.

## Fee Management

The following methods are used to manage service fees:

#### `updateServicefeeAmountBasisPoints`

```solidity
/**
 * @notice Updates the fee amount basis points of a service
 * @param serviceId The service identifier
 * @param newfeeAmountBasisPoints The new fee amount basis points
 */
function updateServicefeeAmountBasisPoints(uint256 serviceId, uint16 newfeeAmountBasisPoints) external onlyOwner
```

Updates the fee amount basis points for a service.

*Effects*:
* Updates the fee amount basis points for the service
* Emits `ServiceFulfillmentFeeSet` event

*Requirements*:
* Caller MUST be the owner
* Service MUST exist
* New fee amount basis points MUST be valid (0-10000)

## Events

```solidity
event ServiceAdded(uint256 serviceId, address indexed fulfiller)
```
Emitted when a new service is added to the registry.

```solidity
event ServiceRemoved(uint256 serviceId)
```
Emitted when a service is removed from the registry.

```solidity
event ServiceBeneficiaryUpdated(uint256 serviceId, address beneficiary)
```
Emitted when a service's beneficiary is updated.

```solidity
event ServiceRefAdded(uint256 serviceId, string ref)
```
Emitted when a reference is added to a service.

```solidity
event FulfillerAdded(address fulfiller, uint256 serviceId)
```
Emitted when a fulfiller is added to a service.

```solidity
event ManagerUpdated(address indexed manager)
```
Emitted when the manager address is updated.

## Errors

```solidity
error InvalidAddress(address _address)
```
Emitted when an invalid address is provided.

```solidity
error ServiceAlreadyExists(uint256 serviceId)
```
Emitted when attempting to add a service that already exists.

```solidity
error ServiceDoesNotExist(uint256 serviceId)
```
Emitted when attempting to interact with a service that does not exist.

```solidity
error InvalidfeeAmountBasisPoints(uint16 feeAmountBasisPoints)
```
Emitted when an invalid fee amount basis points is provided.

## Parameterization

* `MAX_FULFILLMENT_FEE_BASIS_POINTS`: The maximum allowed value for fulfillment fee basis points (10000, representing 100%)
* `_serviceRegistry`: Mapping to store services by their ID
* `_serviceRefs`: Mapping to store service references by service ID
* `_serviceFulfillmentFeeBasisPoints`: Mapping to store fulfillment fees basis points by service ID
* `_serviceRefCount`: Mapping to store the count of references for each service
* `_fulfillerServices`: Mapping to store fulfillers and their associated services
* `_fulfillerServiceCount`: Mapping to store the count of services for each fulfiller
* `_serviceCount`: The total number of services
* `_manager`: The manager address

## Constants

```solidity
uint16 public constant MAX_FULFILLMENT_FEE_BASIS_POINTS = 10000
The maximum fee percentage in basis points (100%).
