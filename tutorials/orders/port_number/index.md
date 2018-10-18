---
layout: default
title: Tutorials - PORT_NUMBER Order
youtubeId: jdL__lw7gw4
---


# How to Port a Service Asset from One Carrier to Another

**This tutorial provides step-by-step instructions for creating, confirming, and submitting an order to port a specific service asset to a different carrier.**

Please note that sometimes the technology used by the new carrier's network is different than what is used by the original carrier. In this case, the new carrier may require different device hardware and therefore a new device will need to be ordered. An API call can be made to the **/catalog/devices** resource to obtain a list of eligible devices from which one can be selected. Even if the new carrier supports the existing device, a new SIM card might be required. Therefore, these orders usually require including shipping information in the order request.

<br/>

## Video Tutorial

This video explains how to create and submit an order request to port an existing service asset (including the service number) from one carrier to another carrier.

{% include youtube.html id=page.youtubeId %}

We also recommend browsing all of our tutorials in our Video Library at [http://mobproc.tv](http://mobproc.tv). 

<br />

## Authentication

This API uses the OAuth 2 standard for authentication. Specifically, it supports two grant types: *Implicit* and *Resource Owner Password Credentials*. For details regarding how to use this standard to authenticate when making your API calls, please refer to the  [Security and Authentication]({{site.url}}/concepts/security/) page.

<br />

## Step 1. Build the request body that is required. 

Next, you will need to compile the JSON that will be submitted in the request body. This JSON includes all of the data that the backend system requires to process this order.

For a PORT_NUMBER, this JSON typically includes the following pieces:

* Transaction type (i.e., PORT_NUMBER).

* Service asset ID for the cellular service that is to be ported. This ID can be obtained via the **/assets/service** resource.

* Region ID for the country in which the new device and/or new service will be used. This ID can be obtained via the **/regions** resource.

* The postal code for the zone in which the new device and/or new service will be used. (Note: This is only required when the selected region is the United States of America.)

* Shopping cart object containing the IDs for what is being ordered (i.e., device, plan, etc.). These IDs can be obtained via the catalog-specific resources (i.e., **/catalog/devices**, **/catalog/plans**).
  * An API call can be made to the **/catalog/devices** resource to obtain a list of eligible devices from which the end user may select their new device ID. When making this call, you should pass the following three query parameters: 
    * **transactionType**. The transaction type may be needed by the source system to invoke business logic that is specific to this type of transaction. 
    * **service**. The service parameter will filter the results to NOT return any devices that are solely supported by the current carrier. 
    * **vendor**. The vendor parameter will further filter the list to only return devices that are compatible with a specific carrier (i.e., the new carrier to which you are porting service).
  * Optional feature IDs for a specific plan can be obtained from the **/catalog/plans/{id}** resource.

* All required and optional order properties. Refer to the <a href="{{site.url}}/tutorials/properties">Obtaining Order Properties</a> page for steps how to identify the properties that are relevant for your order. Also, refer to this same page for instructions for <a href="{{site.url}}/tutorials/properties#choicePropertySubmittingOtherValue">submitting an "other/none of the above" value</a>.

* Shipping information. Since port orders frequently require the delivery of a new device or SIM card, shipping information is usually required for users of most Tangoe source systems (but not the Rivermine source system). Refer to the <a href="{{site.url}}/tutorials/addresses">Formatting an Address</a> page for steps how to assemble the shipping components that are required for your order.


Here is an example of what the fully-assembled request body JSON might look like:

```
{
  "orderRequest": {
    "externalOrderNumber": "MM11234",
    "transactionType": "PORT_NUMBER",
    "serviceId": "34415733",
    "regionId": "70144640",
    "postalCode": "06477",
    "shoppingCart": {
      "deviceId": "9194713909",
      "planId": "6568886494", 
      "optionalFeatureIds": ["2082","1399"]
    },
    "properties": [
      {
        "groupId": "MISCELLANEOUS",
        "fields": [
          {
            "id": "CARRIER_ACCT_NUMBER",
            "type": "TEXT",
            "text": "123456789"
          },
          {
            "id": "NAME_ON_INVOICE",
            "type": "TEXT",
            "text": "Peter Edwards"
          },
          {
            "id": "CONTACT_NUMBER",
            "type": "TEXT",
            "text": "2035559360"
          },
          {
            "id": "CURRENT_CARRIER",
            "type": "TEXT",
            "text": "Formerly AT&T Wireless"
          },
          {
            "id": "SERVICE_NUMBER",
            "type": "TEXT",
            "text": "5123440000"
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
        "amount": 141.68,
        "currencyCode": "USD",
        "recurrence": "MONTHLY"
      }
    ],
    "device": {
      "accessMethod": "GSM",
      "companyAssetId": "CompanyAssetID",
      "id": "33585211",
      "macAddress": "1C:0D:88:90",
      "manufacturer": {
        "_meta": {
          "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/Samsung"
        },
        "id": "Samsung",
        "logoUrl": "https://api.tangoe.com/images/manufacturers/Samsung.gif",
        "name": "Samsung"
      },
      "model": "Galaxy Note Pro - Black",
      "serialNumber": {
        "type": "IMEI",
        "value": " 1234567891023456"
      },
      "simId": "830222233333"
    },
    "externalOrderNumber": "MM11234",
    "postalCode": "06477",
    "properties": [
      {
        "fields": [
          {
            "id": "CARRIER_ACCT_NUMBER",
            "label": "Carrier Account Number",
            "text": "123456789",
            "type": "TEXT"
          },
          {
            "id": "NAME_ON_INVOICE",
            "label": "Name on Invoice",
            "text": "Peter Edwards",
            "type": "TEXT"
          },
          {
            "id": "CONTACT_NUMBER",
            "label": "Contact Phone Number",
            "text": "2035559999",
            "type": "TEXT"
          },
          {
            "id": "CURRENT_CARRIER",
            "label": "Current Carrier",
            "text": "Formerly AT&T Wireless",
            "type": "TEXT"
          },
          {
            "id": "SERVICE_NUMBER",
            "label": "Service Number",
            "text": "5123440000",
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
        "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/assets/services/34415733"
      },
      "contractDates": {
        "activated": "2013-03-20T05:00:00Z",
        "end": "2015-03-20T05:00:00Z",
        "lastDeviceUpgrade": "2014-08-21T05:00:00Z",
        "start": "2013-03-20T05:00:00Z"
      },
      "id": "34415733",
      "serviceNumber": "5123440000",
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
          "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/catalog/devices/9194713909"
        },
        "id": "9194713909",
        "imageUrl": "https://api.tangoe.com/images/devices/VER/iphone5_blk_l.jpg",
        "manufacturer": {
          "_meta": {
            "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/Apple"
          },
          "id": "Apple",
          "logoUrl": "https://api.tangoe.com/images/manufacturers/Apple.gif",
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
          "logoUrl": "https://api.tangoe.com/images/carrier/logo_VER.gif",
          "name": "Verizon Wireless"
        }
      },
      "plan": {
        "_meta": {
          "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/catalog/plans/6568886494"
        },
        "id": "6568886494",
        "includedFeatures": [
          {
            "description": "Base Package"
          }
        ],
        "name": "Nationwide for Business Talk Share Plan",
        "optionalFeatures": [
          {
            "description": "Visual Voice Mail",
            "id": "2082",
            "price": {
              "amount": 2.99,
              "currencyCode": "USD",
              "recurrence": "MONTHLY"
            }
          },
          {
            "description": "Wireless Phone Protection (Advanced Devices)",
            "id": "1399",
            "price": {
              "amount": 100,
              "currencyCode": "USD",
              "recurrence": "MONTHLY"
            }
          }
        ],
        "price": {
          "amount": 38.69,
          "currencyCode": "USD",
          "recurrence": "MONTHLY"
        },
        "vendor": {
          "_meta": {
            "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/vendors/98"
          },
          "id": "98",
          "logoUrl": "https://api.tangoe.com/images/carrier/logo_VER.gif",
          "name": "Verizon Wireless"
        }
      },
      "quantity": 0
    },
    "transactionType": "PORT_NUMBER"
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
      "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/orders/8711260"
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
    "dateSubmitted": "2016-04-26T14:45:20Z",
    "externalOrderNumber": "MM11234",
    "orderId": "8711260",
    "orderSegments": {
      "items": [
        {
          "_meta": {
            "hrefHistory": "https://tngo-mobproc.cloudhub.io/mobproc/v1/orders/8711260/history?orderSegment=8711261",
            "hrefShipments": "https://tngo-mobproc.cloudhub.io/mobproc/v1/orders/8711260/shipments?orderSegment=8711261"
          },
          "accessories": [],
          "device": {
            "imageUrl": "https://api.tangoe.com/images/devices/iphone5_blk_l.jpg",
            "manufacturer": {
              "_meta": {
                "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/Apple"
              },
              "id": "Apple",
              "logoUrl": "https://api.tangoe.com/images/manufacturers/Apple.gif",
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
          "orderSegmentId": "8711261",
          "plan": {
            "name": "Nationwide for Business Talk Share Plan",
            "optionalFeatures": [
              {
                "description": "Visual Voice Mail",
                "price": {
                  "amount": 2.99,
                  "currencyCode": "USD",
                  "recurrence": "MONTHLY"
                }
              },
              {
                "description": "Wireless Phone Protection (Advanced Devices)",
                "price": {
                  "amount": 100,
                  "currencyCode": "USD",
                  "recurrence": "MONTHLY"
                }
              }
            ],
            "price": {
              "amount": 38.69,
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
            "logoUrl": "https://api.tangoe.com/images/carrier/logo_VER.gif",
            "name": "Verizon Wireless"
          }
        }
      ]
    },
    "properties": [
      {
        "fields": [
          {
            "id": "CARRIER_ACCT_NUMBER",
            "label": "Carrier Account Number",
            "text": "123456789",
            "type": "TEXT"
          },
          {
            "id": "NAME_ON_INVOICE",
            "label": "Name on Invoice",
            "text": "Peter Edwards",
            "type": "TEXT"
          },
          {
            "id": "CONTACT_NUMBER",
            "label": "Contact Phone Number",
            "text": "2035559999",
            "type": "TEXT"
          },
          {
            "id": "CURRENT_CARRIER",
            "label": "Current Carrier",
            "text": "Formerly AT&T Wireless",
            "type": "TEXT"
          },
          {
            "id": "SERVICE_NUMBER",
            "label": "Service Number",
            "text": "5123440000",            
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
    "serviceNumber": "5123440000",
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
    "transactionType": "PORT_NUMBER"
  },
  "status": "SUCCESS"
} 
```
<br/>