---
layout: default
title: Moving a Service Assignment 
---


# Moving a Service Assignment 

**This tutorial describes the steps for moving the assignment of a service asset, along with the related charges, from one employee to another.**

Every service asset is assigned to an employee or a department. But from time to time, that assignment may change. When it does, some of the historical charges may also need to be reassigned as well. The **/assets/services/{id}/move** endpoint provides a way to do both.

<br />

## Authentication

This API uses the OAuth 2 standard for authentication. Specifically, it supports two grant types: *Implicit* and *Resource Owner Password Credentials*. For details regarding how to use this standard to authenticate when making your API calls, please refer to the  [Security and Authentication]({{site.url}}/concepts/security/) page.

<br />

## Step 1. Set the URI parameter for the order to be approved.

This endpoint requires a URI parameter to identify the service asset you wish to reassign. Therefore, the service asset ID must be injected into the URI when calling this endpoint. For example, if the service asset ID is **123456**:

```
	/assets/service/123456/move
```

<br />

## Step 2. Build the request body that is required. 

Next, you will need to compile the JSON that will be submitted in the request body. This JSON includes all of the data that the backend system requires to process this order.

For an approval transaction, this JSON typically includes the following pieces:

* Identifier for the new assignee (i.e., employee ID or company employee ID): 
	* newOwnerEmployeeId
	* newOwnerCompanyEmployeeId

* Code to indicate what portion of charges should be moved to new assignee:
	* None (MOVE_NO_CHARGES)
	* Previous month only (MOVE_CHARGES_FOR_LAST_MONTH_ONLY)
	* All (MOVE_ALL_CHARGES)

Here is an example of what the fully-assembled request body JSON might look like:

```
{
  "newOwnerCompanyEmployeeId": "barbara.andrews.acme",
  "moveCharges": "MOVE_ALL_CHARGES"
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

If the submission is successful, the API will once again return a response containing JSON with an HTTP status code of 200. The response body will return the service asset detail record, updated to reflect the assignment change.

Here is an example of what the response might look like:

```
{
  "_meta": {
    "href": "http://api.tangoe.com/mobileprocurement/v1/assets/services/99034551"
  },
  "id": "99034551",
  "serviceNumber": "4045553104",
  "status": "ACTIVE",
  "contractDates": {
    "start": "2009-05-24T11:45:00+00:00",
    "end": "2017-05-24T00:00:00+00:00",
    "activated": "2009-05-24T11:45:00+00:00",
    "lastDeviceUpgrade": "2015-05-24T09:00:00+00:00"
  },
  "plan": {
    "name": "Verizon Nationwide for Business Talk Share 450"
  },
  "vendor": {
    "billing": {
      "_meta": {
        "href": "http://api.tangoe.com/mobileprocurement/v1/vendors/8516701"
      },
      "id": "8516701",
      "name": "Verizon Wireless",
      "logoUrl": "http://cdn.tangoe.com/manage/images/vendors/verizonwireless.jpg"
    },
    "current": {
      "_meta": {
        "href": "http://api.tangoe.com/mobileprocurement/v1/vendors/8516701"
      },
      "id": "8516701",
      "name": "Verizon Wireless",
      "logoUrl": "http://cdn.tangoe.com/manage/images/vendors/verizonwireless.jpg"
    }
  },
  "device": {
    "_meta": {
      "href": "http://api.tangoe.com/mobileprocurement/v1/asset/devices/770288091"
    },
    "id": "770288091",
    "manufacturer": {
      "_meta": {
        "href": "http://api.tangoe.com/mobileprocurement/v1/manufacturers/2455765"
      },
      "id": "2455765",
      "name": "Apple",
      "logoUrl": "http://cdn.tangoe.com/manage/images/manufacturers/apple.jpg"
    },
    "model": {
      "id": "2346152",
      "name": "iPhone 6 (16GB) - Space Gray",
      "imageUrl": "http://cdn.tangoe.com/manage/images/models/iphone6.jpg"
    },
    "simId": "89 91 10 1200 00 320451 0",
    "accessMethod": "GSM",
    "macAddress": "00:1C:B3:09:85:15",
    "companyAssetId": "90004008412334",
    "serialNumber": {
      "type": "IMEI",
      "value": "012345678901234"
    },
    "orderActions": [
      "UPGRADE",
      "ORDER_ACCESSORIES",
      "SWAP_DEVICES"
    ]
  },
  "employee": {
    "_meta": {
      "href": "http://api.tangoe.com/mobileprocurement/v1/employees/123456781"
    },
    "id": "123456781",
    "companyEmployeeId": "barbara.andrews.acme",
    "firstName": "Barbara",
    "lastName": "Andrews",
    "status": "ACTIVE",
    "department": {
      "id": "550148",
      "name": "Finance"
    },
    "email": "barbara.andrews@acme.com"
  },
  "orderActions": [
    "UPGRADE",
    "PORT_NUMBER",
    "UPDATE_FEATURES",
    "SUSPEND",
    "UNSUSPEND",
    "DEACTIVATE"
  ]
}
```
<br/>