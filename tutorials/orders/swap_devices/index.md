---
layout: default
title: Tutorials - SWAP_DEVICE Order
youtubeId: 5ojaJLmh2wU
---


# How to Transfer Service from One Device to Another

**This tutorial provides step-by-step instructions for changing the assignment of a service from one device to another (i.e., swapping devices). Please note that both devices must be known assets in the possession of the user. In other words, service cannot be transferred to a brand new device that is purchased during the order process.**

<br/>

## Video Tutorial

This video explains how to create and submit an order request to change the assignment of a service asset from one device to another.

{% include youtube.html id=page.youtubeId %}

We also recommend browsing all of our tutorials in our Video Library at [http://mobproc.tv](http://mobproc.tv). 

<br />

## Authentication

This API uses the OAuth 2 standard for authentication. Specifically, it supports two grant types: *Implicit* and *Resource Owner Password Credentials*. For details regarding how to use this standard to authenticate when making your API calls, please refer to the  [Security and Authentication]({{site.url}}/concepts/security/) page.

<br />

## Step 1. Build the request body that is required. 

Next, you will need to compile the JSON that will be submitted in the request body. This JSON includes all of the data that the backend system requires to process this order.

For a SWAP_DEVICE  transaction, this JSON typically includes the following pieces:

* Transaction type (i.e., SWAP_DEVICE).

* Service asset ID for the cellular service that is to be transferred. This ID can be obtained via the **/assets/service** resource.

* Manufacturer and model IDs, along with the serial number type and value, of the device to which you wish to transfer the service (i.e., the "newDevice" property). If the manufacturer and model are not currently identified in the device catalog, then the make and model names can passed via otherNameModelName property (e.g., "Acme StarPhone 2000").
  * In addition to the serial number value, the serial number type must also be identified. Supported types include: 
    * IMEI - International Mobile Station Equipment Identity
    * MEID - Mobile Equipment Identifier
    * ESN - Electronic Serial Number
    * CAP_CODE - UK Code of Non-broadcast Advertising, Sales Promotion and Direct Marketing
    * PIN - Personal Identification Number
    * MSN - Mobile Serial Number
    * UNKNOWN

* The same manufacturer and model information must also be passed for the device you are currently using. That is, the device from which you are transferring service (i.e., the "existingDevice" property). Furthermore, the SIM ID for this existing device is also required.

* All required and optional order properties. Refer to the <a href="{{site.url}}/tutorials/properties">Obtaining Order Properties</a> page for steps how to identify the properties that are relevant for your order. Also, refer to this same page for instructions for <a href="{{site.url}}/tutorials/properties#choicePropertySubmittingOtherValue">submitting an "other/none of the above" value</a>.


Here is an example of what the fully-assembled request body JSON might look like:

```
{
  "orderRequest": {
    "externalOrderNumber": "AZ99087547-001",
    "transactionType": "SWAP_DEVICES",
    "serviceId": "38382349",
    "newDevice": {
      "manufacturerId": "Apple",
      "modelId": "iPhone 6 (64GB) - Space Gray",
      "otherMakeModelName": "",
      "serialNumber": {
        "type": "IMEI",
        "value": "123456789001230"
      }
    },
    "existingDevice": {
      "manufacturerId": "Other",
      "modelId": "Other",
      "otherMakeModelName": "Acme StarPhone 2000",
      "serialNumber": {
        "type": "IMEI",
        "value": "012345678901007"
      },
      "simId": "89 91 10 1200 00 320451 3"
    },    
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
            "text": "Please confirm serial number for new device"
          },
          {
            "id": "SERVICE_NUMBER",
            "type": "TEXT",
            "text": "2035557676"
          },
          {
            "id": "PREFERRED_AREA_CODE",
            "type": "TEXT",
            "text": "203"
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
        "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/employees/28673732"
      },
      "companyEmployeeId": "mike.mcpadden.xx1",
      "department": {
        "id": "11569020385",
        "name": "Development"
      },
      "email": "michael.mcpadden@tangoe.com",
      "firstName": "Mike",
      "id": "28673732",
      "lastName": "McPadden",
      "officePhone": "2035559999",
      "status": "ACTIVE"
    },
    "device": {
      "accessMethod": "CDMA",
      "companyAssetId": "123ABC",
      "id": "35362368",
      "macAddress": "90:33:20:94:31",
      "manufacturer": {
        "_meta": {
          "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/LG"
        },
        "id": "LG",
        "logoUrl": "https://cdn.tangoe.com/manage/images/manufacturers/LG.gif",
        "name": "LG"
      },
      "model": "Cookie Plus",
      "serialNumber": {
        "type": "ESN",
        "value": "4289183847832"
      },
      "simId": "83924758342478392342"
    },
    "existingDevice": {
      "manufacturer": {
        "_meta": {
          "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/Other"
        },
        "id": "Other",
        "logoUrl": "https://cdn.tangoe.com/manage/images/manufacturers/Other.gif",
        "name": "Other"
      },
      "model": {
        "_meta": {
          "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/Other/models/Other"
        },
        "id": "Other",
        "name": "Other"
      },
      "otherMakeModelName": "Acme StarPhone 2000",
      "serialNumber": {
        "type": "IMEI",
        "value": "012345678901007"
      },
      "simId": "89 91 10 1200 00 320451 3"
    },
    "externalOrderNumber": "AZ99087547-001",
    "newDevice": {
      "manufacturer": {
        "_meta": {
          "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/Apple"
        },
        "id": "Apple",
        "logoUrl": "https://cdn.tangoe.com/manage/images/manufacturers/Apple.gif",
        "name": "Apple"
      },
      "model": {
        "_meta": {
          "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/Apple/models/Apple"
        },
        "id": "iPhone 6 (64GB) - Space Gray",
        "name": "iPhone 6 (64GB) - Space Gray"
      },
      "serialNumber": {
        "type": "IMEI",
        "value": "123456789001230"
      }
    },
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
            "text": "Please confirm serial number for new device",
            "type": "TEXT"
          },
          {
            "id": "SERVICE_NUMBER",
            "label": "Service Number",
            "text": "2035557676",
            "type": "TEXT"
          },
          {
            "id": "PREFERRED_AREA_CODE",
            "label": "Preferred Area Code",
            "text": "203",
            "type": "TEXT"
          }
        ],
        "groupId": "MISCELLANEOUS"
      }
    ],
    "region": {
      "_meta": {
        "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/regions/70144640"
      },
      "id": "70144640",
      "name": "United States"
    },
    "requestedBy": {
      "_meta": {
        "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/employees/28673732"
      },
      "companyEmployeeId": "mike.mcpadden.xx1",
      "department": {
        "id": "11569020385",
        "name": "Development"
      },
      "email": "michael.mcpadden@tangoe.com",
      "firstName": "Mike",
      "id": "28673732",
      "lastName": "McPadden",
      "officePhone": "2035559999",
      "status": "ACTIVE"
    },
    "service": {
      "_meta": {
        "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/assets/services/38382349"
      },
      "contractDates": {
        "activated": "2010-01-20T06:00:00Z",
        "end": "2016-01-21T06:00:00Z",
        "lastDeviceUpgrade": "2014-01-21T06:00:00Z",
        "start": "2014-01-21T06:00:00Z"
      },
      "id": "38382349",
      "serviceNumber": "2035557676",
      "status": "ACTIVE"
    },
    "transactionType": "SWAP_DEVICES"
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
      "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/orders/8712220"
    },
    "accountHolder": {
      "_meta": {
        "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/employees/28673732"
      },
      "companyEmployeeId": "mike.mcpadden.xx1",
      "department": {
        "id": "11569020385",
        "name": "Development"
      },
      "email": "michael.mcpadden@tangoe.com",
      "firstName": "Mike",
      "id": "28673732",
      "lastName": "McPadden",
      "officePhone": "2035559999",
      "status": "ACTIVE"
    },
    "dateSubmitted": "2016-02-01T15:18:41Z",
    "externalOrderNumber": "AZ99087547-001",
    "orderId": "8712220",
    "orderSegments": {
      "items": [
        {
          "_meta": {
            "hrefHistory": "https://tngo-mobproc.cloudhub.io/mobproc/v1/orders/8712220/history?orderSegment=8712221",
            "hrefShipments": "https://tngo-mobproc.cloudhub.io/mobproc/v1/orders/8712220/shipments?orderSegment=8712221"
          },
          "accessories": [],
          "features": [],
          "orderSegmentId": "8712221",
          "plan": {
            "optionalFeatures": []
          },
          "status": "ORDER_SUBMITTED",
          "vendor": {
            "_meta": {
              "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/vendors/98"
            },
            "id": "98",
            "logoUrl": "https://cdn.tangoe.com/manage/images/carrier/logo_VER.gif",
            "name": "Verizon Wireless"
          }
        }
      ]
    },
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
            "text": "Please confirm serial number for new device",
            "type": "TEXT"
          },
          {
            "id": "SERVICE_NUMBER",
            "label": "Service Number",
            "text": "2035557676",
            "type": "TEXT"
          },
          {
            "id": "PREFERRED_AREA_CODE",
            "label": "Preferred Area Code",
            "text": "203",
            "type": "TEXT"
          }
        ],
        "groupId": "MISCELLANEOUS"
      }
    ],
    "requestedBy": {
      "_meta": {
        "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/employees/28673732"
      },
      "companyEmployeeId": "mike.mcpadden.xx1",
      "department": {
        "id": "11569020385",
        "name": "Development"
      },
      "email": "michael.mcpadden@tangoe.com",
      "firstName": "Mike",
      "id": "28673732",
      "lastName": "McPadden",
      "officePhone": "2035559999",
      "status": "ACTIVE"
    },
    "serviceNumber": "2035557676",
    "shipTo": {
      "address": []
    },
    "status": "ORDER_SUBMITTED",
    "transactionType": "SWAP_DEVICES"
  },
  "status": "SUCCESS"
}
```
<br/>
