---
layout: default
title: Tutorials - DEACTIVATE Order
youtubeId: W79c4NFhABc
---


# How to Deactivate a Service Asset

**This tutorial provides step-by-step instructions for creating, confirming, and submitting an order to deactivate a specific service asset.**

<br/>

## Video Tutorial

This video explains how to create and submit an order request to suspend, unsuspend, or deactivate a service asset.

{% include youtube.html id=page.youtubeId %}

We also recommend browsing all of our tutorials in our Video Library at [http://mobproc.tv](http://mobproc.tv). 

<br />

## Authentication

This API uses the OAuth 2 standard for authentication. Specifically, it supports two grant types: *Implicit* and *Resource Owner Password Credentials*. For details regarding how to use this standard to authenticate when making your API calls, please refer to the  [Security and Authentication]({{site.url}}/concepts/security/) page.

<br />

## Step 1. Build the request body that is required. 

Next, you will need to compile the JSON that will be submitted in the request body. This JSON includes all of the data that the backend system requires to process this order.

For a DEACTIVATE transaction, this JSON typically includes the following pieces:

* Transaction type (i.e., DEACTIVATE).

* Service asset ID for the cellular service that is to be deactivated. This ID can be obtained via the **/assets/service** resource.

* All required and optional order properties. Refer to the <a href="{{site.url}}/tutorials/properties">Obtaining Order Properties</a> page for steps how to identify the properties that are relevant for your order. Also, refer to this same page for instructions for <a href="{{site.url}}/tutorials/properties#choicePropertySubmittingOtherValue">submitting an "other/none of the above" value</a>.


Here is an example of what the fully-assembled request body JSON might look like:

```
{
  "orderRequest": {
    "externalOrderNumber": "AZ99087547-001",
    "transactionType": "DEACTIVATE",
    "serviceId": "31647891",
    "properties": [
      {
        "groupId": "MISCELLANEOUS",
        "fields": [
          {
            "id": "CONTACT_NUMBER",
            "type": "TEXT",
            "text": "2035559999"
          },
          {
            "id": "ADDITIONAL_INSTRUCTIONS",
            "type": "TEXT",
            "text": "Please confirm with account holder that service has been successfully deactivated."
          },
          {
            "id": "PREFERRED_DEACTIVATION_DATE",
            "type": "DATE",
            "date": "2016-12-28T00:00:00+00:00"
          },
          {
            "id": "SERVICE_NUMBER",
            "type": "TEXT",
            "text": "2035551111"
          },
          {
            "id": "Reason",
            "type": "CHOICE",
            "choice": {
              "id": "1"
            }
          }
        ]
      }
    ]
  }
}
```
<br />


## Step 2. Set required request headers.

There are multiple HTTP headers that may be passed as part of your API request. They are used to confirm that the caller is authorized to make this request, along with optional filtering (when appropriate). See the [Request Headers]({{site.url}}/concepts/headers/) page for more information about our supported headers.

### Required Headers:

* **X-TNGO-TENANT** - Used to identify the specific tenant (i.e., customer) for which the API is being called.

* **client_id** - Used to identify your client application as the one calling the API.

### Optional Headers:

* **X-TNGO-CONTEXT-COMPANYEMPLOYEEID** -- The employee ID assigned by the tenant/customer (e.g., employee’s email address, etc.). Used to change context. Refer to the [Setting Context]({{site.url}}/concepts/actor/) page for additional instructions.

* **X-TNGO-CONTEXT-EMPLOYEEID** -- The employee ID assigned by Tangoe. Used to change context. Refer to the [Setting Context]({{site.url}}/concepts/actor/) page for additional instructions.

* **X-TNGO-CONTEXT-HIERARCHYID** -- The Tangoe-assigned ID that is used to specify the organizational hierarchy to be used for the API call.

<br />


## Step 3. Indicate if you wish to obtain an order confirmation.

Prior to submitting the order for vendor processing, you will probably want to first obtain an order confirmation. The confirmation returns all of the IDs passed in with the request body, along with the human-readable data that fully describes what was referenced by those IDs (e.g., device name, vendor, price). This is useful for API consumers who wish to present the order request back to their end user so they can verify it for accuracy.

To obtain a confirmation, set the **confirm** query parameter to true.

<br />

## Step 4. Obtain the order confirmation by calling the /orders resource via HTTP POST. 

Your request, containing the JSON request body and confirm query parameter set to true, is now ready. Submit it via the HTTP POST method to the **/orders** resource.

If successful, the API will return a response with a 200 HTTP status code and containing JSON that fully describes the identifiers that were submitted. This data can then be presented back to the end user for verification and correction (if necessary). 

Here is an example of what the order confirmation response might look like:

```
{
  "orderConfirmation": {
    "accountHolder": {
      "_meta": {
        "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/employees/28671524"
      },
      "companyEmployeeId": "peter.edwards.acme",
      "department": {
        "id": "10652965372",
        "name": "Accounting"
      },
      "email": "peter.edwards@acme.com",
      "firstName": "Peter",
      "id": "28671599",
      "lastName": "Edwards",
      "officePhone": "203-555-9999",
      "status": "ACTIVE"
    },
    "externalOrderNumber": "AZ99087547-001",
    "properties": [
      {
        "fields": [
          {
            "id": "CONTACT_NUMBER",
            "label": "Contact Phone Number",
            "text": "2035559999",
            "type": "TEXT"
          },
          {
            "id": "ADDITIONAL_INSTRUCTIONS",
            "label": "Additional Instructions",
            "text": "Please confirm with account holder that service has been successfully deactivated.",
            "type": "TEXT"
          },
          {
            "date": "2016-12-28T00:00:00Z",
            "id": "PREFERRED_DEACTIVATION_DATE",
            "type": "DATE"
          },
          {
            "id": "SERVICE_NUMBER",
            "label": "Service Number",
            "text": "2035551111",
            "type": "TEXT"
          },
          {
            "choice": {
              "id": "1"
            },
            "id": "Reason",
            "type": "CHOICE"
          }
        ],
        "groupId": "MISCELLANEOUS"
      }
    ],
    "requestedBy": {
      "_meta": {
        "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/employees/28671524"
      },
      "companyEmployeeId": "peter.edwards.acme",
      "department": {
        "id": "10652965372",
        "name": "Accounting"
      },
      "email": "peter.edwards@acme.com",
      "firstName": "Peter",
      "id": "28671599",
      "lastName": "Edwards",
      "officePhone": "203-555-9999",
      "status": "ACTIVE"
    },
    "service": {
      "_meta": {
        "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/assets/services/31647891"
      },
      "contractDates": {
        "activated": "2013-06-05T05:00:00Z",
        "end": "2017-06-05T05:00:00Z",
        "lastDeviceUpgrade": "2013-06-05T05:00:00Z",
        "start": "2013-06-05T05:00:00Z"
      },
      "id": "31647891",
      "serviceNumber": "2035551111",
      "status": "ACTIVE"
    },
    "transactionType": "DEACTIVATE"
  },
  "status": "SUCCESS"
}

```
<br/>


## Step 5. Submit order request.

Once the request has been confirmed, it can be re-submitted for processing via HTTP POST. Before submitting, be sure to set the confirm query parameter to false (or remove it, since the default is false). 

If the submission is successful, the API will once again return a response containing JSON with an HTTP status code of 200. This response also will include the newly-assigned order number (i.e., orderId). 
 
Here is an example of what the order response might look like:

```
{
  "order": {
    "_meta": {
      "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/orders/8054738"
    },
    "accountHolder": {
      "companyEmployeeId": "peter.edwards.acme",
      "department": {
        "id": "10652965372",
        "name": "Accounting"
      },
      "email": "peter.edwards@acme.com",
      "firstName": "Peter",
      "id": "28671599",
      "lastName": "Edwards",
      "officePhone": "203-555-9999",
      "status": "ACTIVE"
    },
    "dateSubmitted": "2015-12-16T19:14:04Z",
    "externalOrderNumber": "AZ99087547-001",
    "orderId": "8054738",
    "properties": [
      {
        "fields": [
          {
            "id": "CONTACT_NUMBER",
            "label": "Contact Phone Number",
            "text": "2035559999",
            "type": "TEXT"
          },
          {
            "id": "ADDITIONAL_INSTRUCTIONS",
            "label": "Additional Instructions",
            "text": "Please confirm with account holder that service has been successfully deactivated.",
            "type": "TEXT"
          },
          {
            "date": "2016-12-28T00:00:00Z",
            "id": "PREFERRED_DEACTIVATION_DATE",
            "type": "DATE"
          },
          {
            "id": "SERVICE_NUMBER",
            "label": "Service Number",
            "text": "2035551111",
            "type": "TEXT"
          },
          {
            "choice": {
              "id": "1"
            },
            "id": "Reason",
            "type": "CHOICE"
          }
        ],
        "groupId": "MISCELLANEOUS"
      }
    ],
    "requestedBy": {
      "_meta": {
        "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/employees/28671524"
      },
      "companyEmployeeId": "peter.edwards.acme",
      "department": {
        "id": "10652965372",
        "name": "Accounting"
      },
      "email": "peter.edwards@acme.com",
      "firstName": "Peter",
      "id": "28671599",
      "lastName": "Edwards",
      "officePhone": "203-555-9999",
      "status": "ACTIVE"
    },
    "serviceNumber": "2035551111",
    "status": "ORDER_SUBMITTED",
    "transactionType": "DEACTIVATE"
  },
  "status": "SUCCESS"
}
```
<br/>
