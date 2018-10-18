---
layout: default
title: Tutorials - UPGRADE Order
---


# How to Upgrade a Device and/or Service Plan

**This tutorial provides step-by-step instructions for creating, confirming, and submitting an order to upgrade a specific device and/or service plan.**


Please note that the **existingDevice** block is required for all UPGRADE orders when the API source system is Premium Mobile (a.k.a., Command). It might not be supported on other source systems (i.e, Rivermine).
<br>

### Special Instructions for Upgrading a Plan Only  ###

When upgrading to a new plan only (i.e., not ordering a new device with it), you may still need to include the **shipTo** block because a new SIM card is often required and it will be shipped to the user. Typically this is the case for users connected to the Premium Mobile source system but not for Rivermine. 

Also, please note that various carrier networks do not always use the same cellular transmission technologies (e.g., GSM, CDMA, etc.). When upgrading to a plan offered by a different carrier, your existing device may no longer be supported by the new plan. In such case, the ID for a new (compatible) device will also need to be included in the shopping cart. If a new device is required but not included in the order, an error will be returned.

<br />

## Authentication

This API uses the OAuth 2 standard for authentication. Specifically, it supports two grant types: *Implicit* and *Resource Owner Password Credentials*. For details regarding how to use this standard to authenticate when making your API calls, please refer to the  [Security and Authentication]({{site.url}}/concepts/security/) page.

<br />

## Step 1. Build the request body that is required. 

Next, you will need to compile the JSON that will be submitted in the request body. This JSON includes all of the data that the backend system requires to process this order.

For an UPGRADE transaction, this JSON typically includes the following pieces:

* Transaction type (i.e., UPGRADE).

* Service asset ID for the cellular service, and/or associated device, that you wish to upgrade. This ID can be obtained via the **/assets/service** resource.

* Existing device information for the device you are currently using. This is often required, even when you are only upgrading the plan. This block includes the manufacturer ID, model ID, serial number, serial number type, and SIM ID. (NOTE: Tangoe's Rivermine source system does not support upgrading a device to an existing device.)

* Shopping cart object containing the IDs for what is being ordered (i.e., devices, plans, and/or plan features). These IDs can be obtained via the catalog-specific resources (i.e., **/catalog/devices**, **/catalog/plans**, **/catalog/features**).
  * Optional feature IDs for a specific plan can be obtained from the **/catalog/plans/{id}** resource.
  * Please note that when upgrading to a plan offered by a different carrier, the new carrier's cellular network may use an different channel access method than your existing carrier (e.g., CDMA, GSM, etc.). In such case, your existing device will not be compatible with the new plan, so a new device must be selected as part of this order.

* All required and optional order properties. Refer to the <a href="{{site.url}}/tutorials/properties">Obtaining Order Properties</a> page for steps how to identify the properties that are relevant to your order. Also, refer to this same page for instructions for <a href="{{site.url}}/tutorials/properties#choicePropertySubmittingOtherValue">submitting an "other/none of the above" value</a>.

* Shipping information. This is required whenever an order includes physical items. Refer to the <a href="{{site.url}}/tutorials/addresses">Formatting an Address</a> page for steps how to assemble the shipping components that are required for your order.


Here is an example of what the fully-assembled request body JSON might look like when upgrading both a device and its related service plan:

```
{
  "orderRequest": {
    "externalOrderNumber": "12345678",
    "transactionType": "UPGRADE",
    "serviceId": "38382349",
    "existingDevice": {
      "manufacturerId": "LG",
      "modelId": "Cookie Plus",      
      "serialNumber": {
        "type": "ESN",
        "value": "4289183847832"
      },
      "simId": "83924758342478392342"
    },
    "shoppingCart": { 
      "deviceId": "9194713904",
      "planId": "6568886698", 
      "optionalFeatureIds": ["122","293"]
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
      "id": "SERVICE_NUMBER",
      "type": "TEXT",
      "text": "2035557676"
    },
    {
      "id": "Reason",
      "type": "CHOICE",
      "choice": {
        "id": "21"
      }
          } 
        ]
      }
    ],    
    "shipTo": { 
      "name": "Peter Edwards",
      "regionId": "70144640",
      "address": [
        {
        "id": "LINE_1",
        "value": "35 Executive Blvd"
        },
        {
        "id": "CITY",
        "value": "Orange"
        },
        {
        "id": "STATE",
        "value": "CT"
        },
        {
        "id": "POSTAL_CODE",
        "value": "06477"
        }
      ],
      "expedite": true
    }
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
      "companyEmployeeId": "peter.edwards.acme",
      "department": {
        "id": "11569020385",
        "name": "Development"
      },
      "email": "peter.edwards@acme.com",
      "firstName": "Peter",
      "id": "28673732",
      "lastName": "Edwards",
      "officePhone": "2035559999",
      "status": "ACTIVE"
    },
    "costSummary": [
      {
        "amount": 199.99,
        "currencyCode": "USD",
        "recurrence": "ONETIME"
      },
      {
        "amount": 558.67,
        "currencyCode": "USD",
        "recurrence": "MONTHLY"
      }
    ],
    "device": {
      "accessMethod": "CDMA",
      "companyAssetId": "Peter's Cookie",
      "id": "35362368",
      "macAddress": "90:33:20:94:31",
      "manufacturer": {
        "_meta": {
          "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/LG"
        },
        "id": "LG",
        "logoUrl": "https://cdn.tangoe.com//images/manufacturers/LG.gif",
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
          "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/LG"
        },
        "id": "LG",
        "logoUrl": "https://cdn.tangoe.com//images/manufacturers/LG.gif",
        "name": "LG"
      },
      "model": {
        "_meta": {
          "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/LG/models/Cookie%20Plus"
        },
        "id": "Cookie Plus",
        "name": "Cookie Plus"
      },
      "serialNumber": {
        "type": "ESN",
        "value": "4289183847832"
      },
      "simId": "83924758342478392342"
    },
    "externalOrderNumber": "12345678",
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
            "id": "SERVICE_NUMBER",
            "label": "Service Number",
            "text": "2035557676",
            "type": "TEXT"
          },
          {
            "choice": {
              "id": "21"
            },
            "id": "Reason",
            "type": "CHOICE"
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
      "companyEmployeeId": "peter.edwards.acme",
      "department": {
        "id": "11569020385",
        "name": "Development"
      },
      "email": "peter.edwards@acme.com",
      "firstName": "Peter",
      "id": "28673732",
      "lastName": "Edwards",
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
    "shipTo": {
      "address": [
        {
          "id": "LINE_1",
          "label": "Address Line1",
          "value": "35 Executive Blvd"
        },
        {
          "id": "CITY",
          "label": "City",
          "value": "Orange"
        },
        {
          "id": "STATE",
          "label": "State",
          "value": "CT"
        },
        {
          "id": "POSTAL_CODE",
          "label": "Zip Code",
          "value": "06477"
        }
      ],
      "expedite": true,
      "name": "Peter Edwards",
      "region": {
        "_meta": {
          "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/regions/70144640"
        },
        "id": "70144640",
        "name": "United States"
      }
    },
    "shoppingCart": {
      "device": {
        "_meta": {
          "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/catalog/devices/9194713904"
        },
        "id": "9194713904",
        "imageUrl": "https://cdn.tangoe.com//images/devices/VER/iphone5_blk_l.jpg",
        "manufacturer": {
          "_meta": {
            "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/Apple"
          },
          "id": "Apple",
          "logoUrl": "https://cdn.tangoe.com//images/manufacturers/Apple.gif",
          "name": "Apple"
        },
        "name": "Apple iPhone 5 (16GB) - Black",
        "price": {
          "amount": 199.99,
          "currencyCode": "USD",
          "recurrence": "ONETIME"
        },
        "vendor": {
          "_meta": {
            "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/vendors/98"
          },
          "id": "98",
          "logoUrl": "https://cdn.tangoe.com//images/carrier/logo_VER.gif",
          "name": "Verizon Wireless"
        }
      },
      "plan": {
        "_meta": {
          "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/catalog/plans/6568886698"
        },
        "id": "6568886698",
        "includedFeatures": [
          {
            "description": "Total Equipment Coverage"
          },
          {
            "description": "VZ Navigator"
          },
          {
            "description": "Base Package"
          }
        ],
        "name": "Nationwide for Business Talk Share Plan",
        "optionalFeatures": [
          {
            "description": "Voice Mail",
            "id": "122",
            "price": {
              "amount": 300,
              "currencyCode": "USD",
              "recurrence": "MONTHLY"
            }
          },
          {
            "description": "Roadside Assistance",
            "id": "293",
            "price": {
              "amount": 200,
              "currencyCode": "USD",
              "recurrence": "MONTHLY"
            }
          }
        ],
        "price": {
          "amount": 58.67,
          "currencyCode": "USD",
          "recurrence": "MONTHLY"
        },
        "vendor": {
          "_meta": {
            "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/vendors/98"
          },
          "id": "98",
          "logoUrl": "https://cdn.tangoe.com//images/carrier/logo_VER.gif",
          "name": "Verizon Wireless"
        }
      },
      "quantity": 0
    },
    "transactionType": "UPGRADE"
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
      "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/orders/8711720"
    },
    "accountHolder": {
      "_meta": {
        "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/employees/28673732"
      },
      "companyEmployeeId": "peter.edwards.acme",
      "department": {
        "id": "11569020385",
        "name": "Development"
      },
      "email": "peter.edwards@acme.com",
      "firstName": "Peter",
      "id": "28673732",
      "lastName": "Edwards",
      "officePhone": "2035559999",
      "status": "ACTIVE"
    },
    "dateSubmitted": "2016-04-28T18:10:27Z",
    "externalOrderNumber": "12345678",
    "orderId": "8711720",
    "orderSegments": {
      "items": [
        {
          "_meta": {
            "hrefHistory": "https://tngo-mobproc.cloudhub.io/mobproc/v1/orders/8711720/history?orderSegment=8711721",
            "hrefShipments": "https://tngo-mobproc.cloudhub.io/mobproc/v1/orders/8711720/shipments?orderSegment=8711721"
          },
          "accessories": [],
          "device": {
            "imageUrl": "https://cdn.tangoe.com//images/devices/iphone5_blk_l.jpg",
            "manufacturer": {
              "_meta": {
                "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/Apple"
              },
              "id": "Apple",
              "logoUrl": "https://cdn.tangoe.com//images/manufacturers/Apple.gif",
              "name": "Apple"
            },
            "name": "Apple iPhone 5 (16GB) - Black",
            "price": {
              "amount": 199.99,
              "currencyCode": "USD",
              "recurrence": "ONETIME"
            }
          },
          "features": [],
          "orderSegmentId": "8711721",
          "plan": {
            "name": "Nationwide for Business Talk Share Plan",
            "optionalFeatures": [
              {
                "description": "Roadside Assistance",
                "price": {
                  "amount": 200,
                  "currencyCode": "USD",
                  "recurrence": "MONTHLY"
                }
              },
              {
                "description": "Voice Mail",
                "price": {
                  "amount": 300,
                  "currencyCode": "USD",
                  "recurrence": "MONTHLY"
                }
              }
            ],
            "price": {
              "amount": 58.67,
              "currencyCode": "USD",
              "recurrence": "MONTHLY"
            }
          },
          "status": "ORDER_SUBMITTED",
          "vendor": {
            "_meta": {
              "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/vendors/98"
            },
            "id": "98",
            "logoUrl": "https://cdn.tangoe.com//images/carrier/logo_VER.gif",
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
            "id": "SERVICE_NUMBER",
            "label": "Service Number",
            "text": "2035557676",
            "type": "TEXT"
          },
          {
            "choice": {
              "id": "21"
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
        "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/employees/28673732"
      },
      "companyEmployeeId": "peter.edwards.acme",
      "department": {
        "id": "11569020385",
        "name": "Development"
      },
      "email": "peter.edwards@acme.com",
      "firstName": "Peter",
      "id": "28673732",
      "lastName": "Edwards",
      "officePhone": "2035559999",
      "status": "ACTIVE"
    },
    "serviceNumber": "2035557676",
    "shipTo": {
      "address": [
        {
          "id": "LINE_1",
          "label": "Address Line1",
          "value": "35 Executive Blvd"
        },
        {
          "id": "CITY",
          "label": "City",
          "value": "Orange"
        },
        {
          "id": "STATE",
          "label": "State",
          "value": "CT"
        },
        {
          "id": "POSTAL_CODE",
          "label": "Zip Code",
          "value": "06477"
        }
      ],
      "expedite": true,
      "name": "Peter Edwards",
      "region": {
        "_meta": {
          "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/regions/70144640"
        },
        "id": "70144640",
        "name": "United States"
      }
    },
    "status": "ORDER_SUBMITTED",
    "transactionType": "UPGRADE"
  },
  "status": "SUCCESS"
}
```
<br/>

