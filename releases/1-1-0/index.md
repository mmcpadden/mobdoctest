---
layout: default
title: Release 1.1.0
---


# Release 1.1.0


<br/>

## New Features


### Porting an Existing Service to a Different Carrier (i.e., porting a number)
This feature enables the creation of an order to port an existing service asset, including the service number, from one carrier to another. For a tutorial explaining how to use this feature, please refer to the [Approving or Rejecting an Order]({{site.url}}/tutorials/orders/approvals/) page. 

To support this feature, the following endpoints were created or modified:

* **/vendors** – Added the **type** filter to the request. This is enables filtering the response to only return carriers. Uses cases for such data includes populating a carrier selection list. 
* **/vendors/{id}** – Added the **accessMethod** property to the response. This is used by the API caller to determine if a specific carrier supports a technology that is compatible with a specific device.

<br/>

### Approving an Order
This feature enables the approving of a specific order. To support this, the following endpoints were created or modified:

* **/orders/{id}/approve** – This new endpoint is used to approve or reject an order that is currently pending approval.
* **/orders [GET]** - Added the **dateTimeUpdated** filter. Also added the **UPDATED_DATE** sort option.
* **/orders [POST]** and **/orders/{id}** - Added the **updated** property.
* **/me** – Added the **isApprover** property to the response. This property returns a boolean. 

<br/>

### Tracking, Sorting and Filtering for when Assets were Last Updated 
This feature is useful for determining when an asset was last updated, as well as support for filtering and sorting on the last updated datetime.

* Device and service asset responses now include the **updated** property.
* Device and service asset collections can be sorted by **UPDATED_DATE**.
* Device and service asset collections can now be filtered by **dateTimeUpdated**.

<br/>

### Moving a Service
This feature enables moving the assignment of charges associated with a specific service asset from one employee to another. For a tutorial explaining how to use this feature, please refer to the [Moving a Service]({{site.url}}/tutorials/move/) page.

To support this feature, the following endpoints were created or modified:

* **/assets/services/{id}/move** – This new endpoint is used to execute a "move" operation. 
* **/me** – Added the **canMoveService** property to the response. This property returns a boolean.

<br/>

### Filtering for Accessories that are Compatible with a Specific Device Asset
The **compatibleWithDeviceAssetId** filter parameter was added to the **/catalog/accessories** endpoint. Previously, API consumers could only filter the accessories catalog by a device catalog ID. This filter enables filtering by an device asset ID as well.

<br/>

### Custom Employee Attributes
Custom attributes can be associated with an employee record. These attributes are customizable and variable  name/value pairs that are defined by the client company. Furthermore, the client company can also define what specific values are valid for each attribute.  These custom attributes are included within the responses for the **/me** and **/employees/{id}** endpoints.


<br/>
<br/>

## New Features for Premium Mobile 
The following new feature only applies to API consumers who are subscribers to Tangoe's Premium Mobile source system (also known as Command). 

### /diagnostics
New diagnostics will be set with a boolean value to indicate whether or not each of these three Premium Mobile roles are currently assigned:

* **IS_AUTHENTICATED_USER_APPROVER** 
* **IS_ACTOR_APPROVER** 
* **EXECUTE_MOVE_SERVICE_PERMISSION** 


<br/>
<br/>

## Fixed Issues

### /vendors and /vendors/{id} endpoints
The region associated with a vendor that was returned within the response was missing the **_meta** block. The response now returns this metadata.
