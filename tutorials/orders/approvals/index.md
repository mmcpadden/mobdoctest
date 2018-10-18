---
layout: default
title: Approving an Order
---


# Approving or Rejecting an Order

**This tutorial describes the steps for approving or rejecting a specific order that is currently pending approval.**

<br />

## Authentication

This API uses the OAuth 2 standard for authentication. Specifically, it supports two grant types: *Implicit* and *Resource Owner Password Credentials*. For details regarding how to use this standard to authenticate when making your API calls, please refer to the  [Security and Authentication]({{site.url}}/concepts/security/) page.

<br />

## Step 1. Set the URI parameter for the order to be approved.

This resource requires a URI parameter to identify the order you wish to approve. Therefore, the order ID must be injected into the URI when calling this resource. For example, if the order ID is **123456**:

```
	/orders/123456/approval
```

<br />

## Step 2. Build the request body that is required. 

Next, you will need to compile the JSON that will be submitted in the request body. This JSON includes all of the data that the backend system requires to process this order.

For an approval transaction, this JSON typically includes the following pieces:

* Action (i.e., APPROVE or REJECT).

* Reason for approval or rejection. This field is necessary for the successful submission when required by the workflow associated with the order. Typically, a reason is required for a rejection.

Here is an example of what the fully-assembled request body JSON might look like:

```
{
  "action": "APPROVE",
  "reason": "Employee eligible for device upgrade due to promotion to manager"
}
```

<br />

## Step 3. Set required request headers.

There are multiple HTTP headers that may be passed as part of your API request. They are used to confirm that the caller is authorized to make this request, along with optional filtering (when appropriate). See the [Request Headers]({{site.url}}/concepts/headers/) page for more information about our supported headers.

### Required Headers:

* **X-TNGO-TENANT** - Used to identify the specific tenant (i.e., customer) for which the API is being called.

* **client_id** - Used to identify your client application as the one calling the API.

### Optional Headers:

* **X-TNGO-CONTEXT-COMPANYEMPLOYEEID** -- The employee ID assigned by the tenant/customer (e.g., employee’s email address, etc.). Used to change context. Refer to the [Setting Context]({{site.url}}/concepts/actor/) page for additional instructions.

* **X-TNGO-CONTEXT-EMPLOYEEID** -- The employee ID assigned by Tangoe. Used to change context. Refer to the [Setting Context]({{site.url}}/concepts/actor/) page for additional instructions.

<br />

## Step 4. Submit order request.

If the submission is successful, the API will once again return a response containing JSON with an HTTP status code of 200. The response body will simply include one of four status options:

| ***Status*** | ***Description*** |
| --- | --- |
| **APPROVAL_QUEUED**	| The approval request is queued up for the approval workflow. 		|
| **REJECTION_QUEUED**	| The approval request is queued up for the rejection workflow. 	|
| **APPROVAL_NOT_NEEDED**	| Returned when the order has already been approved (or rejected), or when an approval workflow has not been configured for this type of order. 		|
| **NOT_READY_FOR_APPROVAL**	| Returned for orders that do require approval but have not yet entered the AWAITING_APPROVAL state. |

<br />

Here is an example of what the response might look like:

```
{
  "status": "APPROVAL_QUEUED"
}
```
<br/>
