---
layout: default
title: Create and Submit an Order
---

# How to Create and Submit an Order

**This tutorial provides a high-level overview of how orders are created and submitted. Please click on a specific transaction type (below) for step-by-step instructions describing how to successfully create and submit an order for that transaction type.**


Each type of order that can be created with this API can be classified in one of two categories: *Procurement* or *Care*. Procurement transactions are orders for obtaining a new device, plan, and/or accessories. Care transactions are orders for modifying an existing device or service asset. 

Within each of these categories are specific types of transactions. Click on a transaction type in the table below to view the specific steps required to create that type of order, along with examples of the request/response body for that type.

<a name="transactionTypes"></a>
*Click on a Transaction Type below to see specific instructions for submitting that particular type of order.*


| ***Transaction Type*** | ***Category*** | ***Description*** |																	
| --- | --- | --- |
| [NEW_ACTIVATION]({{site.url}}/tutorials/orders/new_activation/) | Procurement  | Obtain a new device, service, and/or accessory. |
| [UPGRADE]({{site.url}}/tutorials/orders/upgrade/) 				 | Procurement | Upgrade an existing device and/or service asset. |
| [PORT_NUMBER]({{site.url}}/tutorials/orders/port_number/) 		 | Procurement | Port a  service asset from one carrier to another. |
| [ORDER_ACCESSORIES]({{site.url}}/tutorials/orders/order_accessories/) | Procurement | Obtain one or more new accessory item(s). |
| [SUSPEND]({{site.url}}/tutorials/orders/suspend/)	 				| Care 	| Suspend an active cellular service asset.	|
| [UNSUSPEND]({{site.url}}/tutorials/orders/unsuspend/) 	 			| Care 	| Reactivate a cellular service asset that was previously suspended. |
| [DEACTIVATE]({{site.url}}/tutorials/orders/deactivate/) 			| Care 	| Deactivate an active cellular service asset.	|
| [UPDATE_FEATURES]({{site.url}}/tutorials/orders/update_features/)	| Care 	| Add or remove optional features on a cellular service asset. 
| [SWAP_DEVICES]({{site.url}}/tutorials/orders/swap_devices/) 		| Care 	| Reassign cellular service from one device to another. |
| [XFER_SERVICE_OUT]({{site.url}}/tutorials/orders/xfer_service_out/) | Care | Transfer ownership of an existing service asset from a corporate account to a personal account. |
| [XFER_SERVICE_IN]({{site.url}}/tutorials/orders/xfer_service_in/) | Care | Transfer ownership of an existing service asset from a personal account to a corporate account. |
| [XFER_SERVICE_IN_AND_PORT]({{site.url}}/tutorials/orders/xfer_service_in_and_port/) | Care | Transfer ownership of an existing service asset from a personal account to a corporate account but related device is not supported by the corporate carrier (i.e., must select a new device). |

<br/> 

In addition to the required request headers, API consumers need to pass a block of JSON in the request body. This JSON code provides all of the data needed by the API to process the order request (e.g., transaction type ID, requester's employee ID, catalog ID of for the device to be obtained, etc.). In addition to the **/orders** resource, the API also provides many other supporting resources that are used to gather all of this data.

Once your order request is prepared, it can be passed to the server for validation and processing. This processing supports two modes: *confirmation* and *submission*:

* **Confirmation** -- When set to confirmation mode, the order will NOT be submitted for fulfillment. Instead, after validating the data in the request, the server will return a response containing a status value of "SUCCESS," along with JSON in the response containing human-readable data that fully describes everything that was referenced by IDs passed within the request body. This expanded data provides an API consumer with enough information to display meaningful request data in the user interface. This data can be presented to the end user to verify the order, correct any errors, and submit for fulfillment.

* **Submission** -- Once the request is successfully validated and without errors---and the confirmation mode is NOT explicitly set to true---the order can be submitted to the server for fulfillment. Again, the server will return a status value of "SUCCESS", along with enough meaningful information for display to the end user, as well as with a new, system-generated order ID.

* **Errors** -- If during validation the server determines that there are issues with the request that prevent it from being processed, the server will return a status value of "ERROR." The response will also include an array containing data describing the error(s). For a detailed description of what might be returned for an unsuccessful response, please go to the [Errors]({{site.url}}/concepts/errors/) page.

<br />


## Steps for Creating and Submitting an Order

All orders require the following process steps in order to create, confirm, and submit an order.

### Step 1. Build the request body (in JSON) that is required for your specific transaction type.
The transaction type identifies the specific action that you wish to take (e.g., obtain a new phone and cellular plan, suspend cellular service, etc.). See the chart above for a <a href="#transactionTypes">full list of available transaction types</a>, along with links to the specific order creation steps required for each transaction type, including JSON samples.
 
### Step 2. Set the request headers needed for your desired transaction.
Please refer to the [Request Headers]({{site.url}}/concepts/headers/) page for details regarding what headers are available and how to configure them.

### Step 3. Indicate if you wish to obtain an order confirmation.
As described in the section above, calling the orders endpoint in "confirmation" mode does not submit it for processing. Rather, it returns all of the IDs passed in with the request body, along with the human-readable data that fully describes what was referenced by those IDs (e.g., device name, vendor, price). This is useful for API consumers who wish to present the order request back to their end user so they can verify it for accuracy.

To indicate confirmation mode, simply set the confirm query parameter to true.

### Step 4. Submit the order request via HTTP POST to the orders resource (/orders).
If this is a confirmation, then the order will not be submitted for processing yet. However, if the confirm query parameter is not passed or it is set to false, then your order will be submitted for processing. 

### Step 5. Your order response is returned. 

* If the order contains validation issues or otherwise could not be accepted, then the response with an appropriate error message will be returned.
* If your order request was successfully submitted for confirmation, the response will include all of the data needed for an end user to verify their order.
* If your order request was successfully submitted for vendor processing, the response will include a Tangoe-assigned ID number.



