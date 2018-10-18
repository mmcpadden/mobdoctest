---
layout: default
title: Release 1.2.1
---


# Release 1.2.1


<br/>

## New Features

### Filter to exclude device or plan order properties
Two filters were added to the **/orders/properties** resource to allow the caller to filter the response to exclude device- or plan-specific properties. Those filters are:

* ***orderExcludesDevice*** – Set to TRUE if placing an UPGRADE order that does NOT include a new device. The collection of order properties returned will not include device-specific properties.
* ***orderExcludesPlan*** – Set to TRUE if placing an UPGRADE order that does NOT include a new plan. The collection of order properties returned will not include plan-specific properties.

<br/>

### Filter to return only orders that are authorized to be approved by a specific employee
Two filters were added to the **/orders** resource to allow the caller to filter the response to only return orders that are awaiting to be approved by a specific employee:

* ***eligibleToApproveEmployeeId*** – Set to the ID (assigned by Tangoe) for the employee who is authorized to approve all of the orders returned in the response.
* ***eligibleToApproveCompanyEmployeeId*** – Set to the ID (assigned by the customer/tenant) for the employee who is authorized to approve all of the orders returned in the response.


<br/>
<br/>

## Fixed Issues

(N/A)
