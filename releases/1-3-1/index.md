---
layout: default
title: Release 1.3.1
---


# Release 1.3.1


<br/>

## New Features

### Additional device details optionally available via the device asset collection 
Two filters were added to the **/assets/devices** resource to allow the caller to receive additional data in the collection response to include device asset properties that were previously only available via the specific device asset resource (i.e., **/assets/devices/{id}**). The two filters are:

* ***includeServiceDetails*** – When set to TRUE, all details in the **service** property of the device asset details will be returned in the response.  
* ***includeUpgradeEligibleDate*** – When this parameter and the includeServiceDetails parameter (above) are both set to TRUE, then the **upgradeEligibleDate** property will be included in the service details for each device asset the collection response.

<br/>

### Additional order details optionally available via the orders collection 
Six filters were added to the **/orders** resource to allow the caller to receive additional data in the collection response to include order detail properties previously only available via the specific order resource (i.e., **/orders/{id}**). The six filters are:

* ***includeOrderProperties*** – When set to TRUE, the **propertyGroup** property will be populated within the order property of the Order (single) response.
* ***includeApprovers*** – When set to TRUE, the **pendingApprovalLevel** and **approver** properties will be populated within the order property of the Order (single) response.
* ***includeOrderSegments*** – When set to TRUE, the **segment** property will be populated within the order property of the Order (single) response.
* ***includeOrderShipments*** – When set to TRUE, the **shipment** property will be populated within the order property of the Order (single) response.
* ***includeOrderHistory*** – When set to TRUE, the **history** property will be populated within the order property of the Order (single) response.
* ***includeShipToAddress*** – When set to TRUE, the **shipTo** property will be populated within the order property of the Order (single) response.


<br/>
<br/>

## Fixed Issues

(N/A)
