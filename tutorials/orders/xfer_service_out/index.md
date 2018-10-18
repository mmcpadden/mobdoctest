---
layout: default
title: Tutorials - XFER_SERVICE_OUT Order
---


# How to Transfer Service from Corporate/Business Ownership to Personal Ownership

**This tutorial provides step-by-step instructions for transferring ownership of an existing service asset from a corporate/business account to a personal account.** 

NOTE: This transaction type may not be supported by all Tangoe backend systems. Please contact your Tangoe representative if you are unable to successfully submit your order request.

<br/>

## Authentication

This API uses the OAuth 2 standard for authentication. Specifically, it supports two grant types: *Implicit* and *Resource Owner Password Credentials*. For details regarding how to use this standard to authenticate when making your API calls, please refer to the  [Security and Authentication]({{site.url}}/concepts/security/) page.

<br />

## Step 1. Build the request body that is required. 

Next, you will need to compile the JSON that will be submitted in the request body. This JSON includes all of the data that the backend system requires to process this order.

For a XFER_SERVICE_OUT, this JSON typically includes the following pieces:

* Transaction type (i.e., XFER_SERVICE_OUT).

* Service asset ID for the cellular service ownership that is to be transferred. This ID can be obtained via the **/assets/service** resource.

* All required and optional order properties. Refer to the <a href="{{site.url}}/tutorials/properties">Obtaining Order Properties</a> page for steps how to identify the properties that are relevant for your order. Also, refer to this same page for instructions for <a href="{{site.url}}/tutorials/properties#choicePropertySubmittingOtherValue">submitting an "other/none of the above" value</a>.


Here is an example of what the fully-assembled request body JSON might look like:

```
{
  "orderRequest": {
    "externalOrderNumber": "AZ99087547-001",
    "serviceId": "38382555",
    "transactionType": "XFER_SERVICE_OUT",
    "properties": [
      {
        "groupId": "MISCELLANEOUS",
        "fields": [
          {
            "id": "CONTACT_NUMBER",
            "type": "TEXT",
            "text": "2035558888"
          },
          {
            "id": "ADDITIONAL_INSTRUCTIONS",
            "type": "TEXT",
            "text": "Please confirm with account holder that service has been successfully transferred"
          },
          {
            "id": "PERSONAL_EMAIL",
            "type": "TEXT",
            "text": "peter.edwards@example.com"
          },
          {
            "id": "SERVICE_NUMBER",
            "type": "TEXT",
            "text": "2035551111"
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

* **Authorization** - Used to obtain access to the API. For all API calls, pass "Bearer &lt;insert access token string&gt;".

### Optional Headers:

* **X-TNGO-CONTEXT-COMPANYEMPLOYEEID** -- The employee ID assigned by the tenant/customer (e.g., employee’s email address, etc.). Used to change context. Refer to the [Setting Context]({{site.url}}/concepts/actor/) page for additional instructions.

* **X-TNGO-CONTEXT-EMPLOYEEID** -- The employee ID assigned by Tangoe. Used to change context. Refer to the [Setting Context]({{site.url}}/concepts/actor/) page for additional instructions.

* **X-TNGO-CONTEXT-HIERARCHYID** -- The Tangoe-assigned ID that is used to specify the organizational hierarchy to be used for the API call.

<br />


## Step 3. Indicate if you wish to obtain an order confirmation.

Prior to submitting the order for vendor processing, you will probably want to first obtain an order confirmation. The confirmation returns all of the IDs passed in with the request body, along with the human-readable data that fully describes what was referenced by those IDs (e.g., device name, vendor, price). This is useful for API consumers who wish to present the order request back to their end user so they can verify it for accuracy.

To obtain a confirmation, set the **confirm** query parameter to true.

<br />

## Step 4. Obtain the order confirmation by calling the /orders endpoint via HTTP POST. 

Your request, containing the JSON request body and confirm query parameter set to true, is now ready. Submit it via the HTTP POST method to the **/orders** endpoint.

If successful, the API will return a response with a 200 HTTP status code and containing JSON that fully describes the identifiers that were submitted. This data can then be presented back to the end user for verification and correction (if necessary). 

Here is an example of what the order confirmation response might look like:

```
{
   "orderConfirmation" : {
      "accountHolder" : {
         "_meta" : {
            "href" : "https://tngo-mobproc.cloudhub.io/mobproc/v1/employees/28691777"
         },
         "companyEmployeeId" : "peter.edwards",
         "department" : {
            "id" : "11569020333",
            "name" : "Development"
         },
         "email" : "peter.edwards@example.com",
         "firstName" : "Peter",
         "id" : "28691777",
         "lastName" : "Edwards",
         "officePhone" : "2035551234",
         "status" : "ACTIVE"
      },
      "acknowledgements" : [ ],
      "device" : {
         "accessMethod" : "GSM",
         "id" : "37948999",
         "macAddress" : "489491231333",
         "manufacturer" : {
            "_meta" : {
               "href" : "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/Apple"
            },
            "id" : "Apple",
            "logoUrl" : "https://cdn.tangoe.com/images/manufacturers/Apple.gif",
            "name" : "Apple"
         },
         "model" : "iPhone 7 Plus (32GB) - Silver",
         "serialNumber" : {
            "type" : "IMEI",
            "value" : "1919191919"
         },
         "simId" : "19191919222"
      },
      "externalOrderNumber" : "AZ99087547-001",
      "properties" : [
        {
          "fields" : [
            {
              "id" : "CONTACT_NUMBER",
              "label" : "Contact Phone Number",
              "text" : "2035558888",
              "type" : "TEXT"
            },
            {
              "id" : "ADDITIONAL_INSTRUCTIONS",
              "label" : "Additional Instructions",
              "text" : "Please confirm with account holder that service has been successfully transferred",
              "type" : "TEXT"
            },
            {
              "id" : "PERSONAL_EMAIL",
              "label" : "Personal E-mail Address",
              "text" : "peter.edwards@example.com",
              "type" : "TEXT"
            },
            {
              "id" : "SERVICE_NUMBER",
              "label" : "Service Number",
              "text" : "2035551111",
              "type" : "TEXT"
            }
          ],
          "groupId" : "MISCELLANEOUS"
        }
      ],
      "region" : {
        "_meta" : {
          "href" : "https://tngo-mobproc.cloudhub.io/mobproc/v1/regions/70144222"
        },
        "id" : "70144222",
        "name" : "United States"
      },
      "requestedBy" : {
        "_meta" : {
          "href" : "https://tngo-mobproc.cloudhub.io/mobproc/v1/employees/28691777"
        },
        "companyEmployeeId" : "peter.edwards",
        "department" : {
          "id" : "11569020333",
          "name" : "Development"
        },
        "email" : "peter.edwards@example.com",
        "firstName" : "Peter",
        "id" : "28691777",
        "lastName" : "Edwards",
        "officePhone" : "2035551234",
        "status" : "ACTIVE"
      },
      "service" : {
        "_meta" : {
          "href" : "https://tngo-mobproc.cloudhub.io/mobproc/v1/assets/services/38382555"
        },
        "contractDates" : {
          "activated" : "2018-06-07T00:00:00Z",
          "end" : "2019-06-07T00:00:00Z",
          "lastDeviceUpgrade" : "2018-06-07T00:00:00Z",
          "start" : "2018-06-07T00:00:00Z"
        },
        "id" : "38382555",
        "serviceNumber" : "2035551111",
        "status" : "ACTIVE"
      },
      "transactionType" : "XFER_SERVICE_OUT"
   },
   "status" : "SUCCESS"
}
```
<br/>


## Step 5. Submit order request.

Once the request has been confirmed, it can be re-submitted for processing via HTTP POST. Before submitting, be sure to set the confirm query parameter to false (or remove it, since the default is false). 

If the submission is successful, the API will once again return a response containing JSON with an HTTP status code of 200. This response also will include the newly-assigned order number (i.e., orderId). 
 
Here is an example of what the order response might look like:

```
{
   "order" : {
      "_meta" : {
         "href" : "https://tngo-mobproc.cloudhub.io/mobproc/v1/orders/13298555"
      },
      "accountHolder" : {
         "_meta" : {
            "href" : "https://tngo-mobproc.cloudhub.io/mobproc/v1/employees/28691777"
         },
         "companyEmployeeId" : "peter.edwards",
         "department" : {
            "id" : "11569020333",
            "name" : "Development"
         },
         "email" : "peter.edwards@example.com",
         "firstName" : "Peter",
         "id" : "28691777",
         "lastName" : "Edwards",
         "officePhone" : "2035551234",
         "status" : "ACTIVE"
      },
      "dateSubmitted" : "2018-09-26T12:30:31Z",
      "externalOrderNumber" : "AZ99087547-001",
      "orderId" : "13298555",
      "orderSegments" : {
         "items" : [
            {
               "_meta" : {
                  "hrefHistory" : "https://tngo-mobproc.cloudhub.io/mobproc/v1/orders/13298555/history?orderSegment=13298556",
                  "hrefShipments" : "https://tngo-mobproc.cloudhub.io/mobproc/v1/orders/13298555/shipments?orderSegment=13298556"
               },
               "accessories" : [ ],
               "features" : [ ],
               "orderSegmentId" : "13298556",
               "status" : "ORDER_SUBMITTED",
               "vendor" : {
                  "_meta" : {
                     "href" : "https://tngo-mobproc.cloudhub.io/mobproc/v1/vendors/111"
                  },
                  "id" : "111",
                  "logoUrl" : "https://cdn.tangoe.com/images/carrier/logo_ATT.gif",
                  "name" : "AT&T Mobility"
               }
            }
         ]
      },
      "properties" : [
         {
            "fields" : [
               {
                  "id" : "CONTACT_NUMBER",
                  "label" : "Contact Phone Number",
                  "text" : "2035558888",
                  "type" : "TEXT"
               },
               {
                  "id" : "ADDITIONAL_INSTRUCTIONS",
                  "label" : "Additional Instructions",
                  "text" : "Please confirm with account holder that service has been successfully transferred",
                  "type" : "TEXT"
               },
               {
                  "id" : "PERSONAL_EMAIL",
                  "label" : "Personal E-mail Address",
                  "text" : "peter.edwards@example.com",
                  "type" : "TEXT"
               },
               {
                  "id" : "SERVICE_NUMBER",
                  "label" : "Service Number",
                  "text" : "2035551111",
                  "type" : "TEXT"
               }
            ],
            "groupId" : "MISCELLANEOUS"
         }
      ],
      "requestedBy" : {
         "_meta" : {
            "href" : "https://tngo-mobproc.cloudhub.io/mobproc/v1/employees/28691777"
         },
         "companyEmployeeId" : "peter.edwards",
         "department" : {
            "id" : "11569020333",
            "name" : "Development"
         },
         "email" : "peter.edwards@example.com",
         "firstName" : "Peter",
         "id" : "28691777",
         "lastName" : "Edwards",
         "officePhone" : "2035551234",
         "status" : "ACTIVE"
      },
      "serviceNumber" : "2035551111",
      "status" : "ORDER_SUBMITTED",
      "transactionType" : "XFER_SERVICE_OUT",
      "updated" : "2018-09-26T12:21:22Z"
   },
   "status" : "SUCCESS"
} 
```
<br/>