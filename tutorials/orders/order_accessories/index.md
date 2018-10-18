---
layout: default
title: Tutorials - ORDER_ACCESSORIES Order
youtubeId: F0Ocvo_NiEk
---


# How to Order Accessories

**This tutorial provides step-by-step instructions for creating, confirming, and submitting an order to procure accessories for use with a specific device asset.**

<br/>

## Video Tutorial

This video explains how to create and submit an order request to order accessories for a specific device asset.

{% include youtube.html id=page.youtubeId %}

We also recommend browsing all of our tutorials in our Video Library at [http://mobproc.tv](http://mobproc.tv). 

<br />

## Authentication

This API uses the OAuth 2 standard for authentication. Specifically, it supports two grant types: *Implicit* and *Resource Owner Password Credentials*. For details regarding how to use this standard to authenticate when making your API calls, please refer to the  [Security and Authentication]({{site.url}}/concepts/security/) page.

<br />

## Step 1. Build the request body that is required. 

Next, you will need to compile the JSON that will be submitted in the request body. This JSON includes all of the data that the backend system requires to process this order.

For an ORDER_ACCESSORIES transaction, this JSON typically includes the following pieces:

* Transaction type (i.e., ORDER_ACCESSORIES).

* Service asset ID for the cellular service associated with the user's device. This ID can be obtained via the **/assets/service** resource.

* Shopping cart object containing an array of IDs for the accessory items being ordered. 
  * These IDs can be obtained via the accessory catalog resource (i.e., **/catalog/accessories**). When calling this resource, you must pass the **service** query parameter to get a list that is filtered to include only the accessories that are compatible with your device.

* All required and optional order properties. Refer to the <a href="{{site.url}}/tutorials/properties">Obtaining Order Properties</a> page for steps how to identify the properties that are relevant for your order. Also, refer to this same page for instructions for <a href="{{site.url}}/tutorials/properties#choicePropertySubmittingOtherValue">submitting an "other/none of the above" value</a>.

* Shipping information. This is required for orders that include physical items. Refer to the <a href="{{site.url}}/tutorials/addresses">Formatting an Address</a> page for steps how to assemble the shipping components that are required to assemble a valid shipping address.

Here is an example of what the fully-assembled request body JSON might look like:

```
{
  "orderRequest": {
    "externalOrderNumber": "MM123456A",
    "transactionType": "ORDER_ACCESSORIES",
    "serviceId": "38382349",
    "shoppingCart": {
      "accessoryIds": ["3628935898","3628939021"]
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
        "amount": 44.98,
        "currencyCode": "USD",
        "recurrence": "ONETIME"
      },
      {
        "amount": 0,
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
    "externalOrderNumber": "MM123456A",
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
      "accessories": [
        {
          "_meta": {
            "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/catalog/accessories/3628935898"
          },
          "id": "3628935898",
          "imageUrl": "https://cdn.tangoe.com//images/devices/VER/MICRDUALVPCF_m.jpg",
          "manufacturer": {
            "_meta": {
              "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/Verizon%20Wireless"
            },
            "id": "Verizon Wireless",
            "logoUrl": "https://cdn.tangoe.com//images/manufacturers/Verizon Wireless.gif",
            "name": "Verizon Wireless"
          },
          "name": "Verizon Wireless Vehicle Charger with Dual Output (MICRDUALVPC-F)",
          "price": {
            "amount": 22.49,
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
        {
          "_meta": {
            "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/catalog/accessories/3628939021"
          },
          "id": "3628939021",
          "imageUrl": "https://cdn.tangoe.com//images/devices/VER/EMICUSBDTVL_F_m.jpg",
          "manufacturer": {
            "_meta": {
              "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/Verizon%20Wireless"
            },
            "id": "Verizon Wireless",
            "logoUrl": "https://cdn.tangoe.com//images/manufacturers/Verizon Wireless.gif",
            "name": "Verizon Wireless"
          },
          "name": "Verizon Wireless Rapid Wall Charger with 6ft Cable for Micro USB (EMICUSBDTVL-F)",
          "price": {
            "amount": 22.49,
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
        }
      ],
      "quantity": 0
    },
    "transactionType": "ORDER_ACCESSORIES"
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
      "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/orders/8711820"
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
    "dateSubmitted": "2016-04-29T21:04:56Z",
    "externalOrderNumber": "MM123456A",
    "orderId": "8711820",
    "orderSegments": {
      "items": [
        {
          "_meta": {
            "hrefHistory": "https://tngo-mobproc.cloudhub.io/mobproc/v1/orders/8711820/history?orderSegment=8711821",
            "hrefShipments": "https://tngo-mobproc.cloudhub.io/mobproc/v1/orders/8711820/shipments?orderSegment=8711821"
          },
          "accessories": [
            {
              "imageUrl": "https://cdn.tangoe.com//images/devices/MICRDUALVPCF_m.jpg",
              "manufacturer": {
                "_meta": {
                  "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/Verizon%20Wireless"
                },
                "id": "Verizon Wireless",
                "logoUrl": "https://cdn.tangoe.com//images/manufacturers/Verizon Wireless.gif",
                "name": "Verizon Wireless"
              },
              "name": "Verizon Wireless Vehicle Charger with Dual Output (MICRDUALVPC-F)",
              "price": {
                "amount": 22.49,
                "currencyCode": "USD",
                "recurrence": "ONETIME"
              }
            },
            {
              "imageUrl": "https://cdn.tangoe.com//images/devices/EMICUSBDTVL_F_m.jpg",
              "manufacturer": {
                "_meta": {
                  "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/Verizon%20Wireless"
                },
                "id": "Verizon Wireless",
                "logoUrl": "https://cdn.tangoe.com//images/manufacturers/Verizon Wireless.gif",
                "name": "Verizon Wireless"
              },
              "name": "Verizon Wireless Rapid Wall Charger with 6ft Cable for Micro USB (EMICUSBDTVL-F)",
              "price": {
                "amount": 22.49,
                "currencyCode": "USD",
                "recurrence": "ONETIME"
              }
            }
          ],
          "features": [],
          "orderSegmentId": "8711821",
          "plan": {
            "optionalFeatures": []
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
    "transactionType": "ORDER_ACCESSORIES"
  },
  "status": "SUCCESS"
}
```
<br/>