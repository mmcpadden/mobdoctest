---
layout: default
title: Tutorials - Ordering - NEW_ACTIVATION 
youtubeId: jGVMTBWMgmk
---


# How to Order a New Device and/or Service Plan 

**This tutorial provides step-by-step instructions for creating, confirming, and submitting an order to procure a new device and service plan (i.e., NEW_ACTIVATION). Please note that device accessories can also be included with this order.**

<br/>

## Video Tutorial

This video explains how to create and submit an order request to procure a new device and service plan (often referred to as a NEW_ACTIVATION). It can also include accessories for the new device that you’re ordering. But please note that this is different than the [ORDER_ACCESSORIES]({{site.url}}/tutorials/orders/order_accessories/) transaction type, which is used to order accessories for an existing device asset.

{% include youtube.html id=page.youtubeId %}

We also recommend browsing all of our tutorials in our Video Library at [http://mobproc.tv](http://mobproc.tv). 

<br />

## Authentication

This API uses the OAuth 2 standard for authentication. Specifically, it supports two grant types: *Implicit* and *Resource Owner Password Credentials*. For details regarding how to use this standard to authenticate when making your API calls, please refer to the  [Authentication and Security]({{site.url}}/concepts/security/) page.

<br />

## Step 1. Build the request body that is required. 

Next, you will need to compile the JSON that will be submitted within the request body. This JSON includes all of the data that the backend system requires to process this order.

For a NEW_ACTIVATION transaction, this JSON typically includes the following pieces:

* Transaction type (i.e., NEW_ACTIVATION).

* Region ID for the country in which the device and/or service will be used. This ID can be obtained via the **/regions** resource.

* The postal code for the zone in which the device and/or service will be used. (Note: this is only required when the selected region is the United States of America.)

* Shopping cart object containing the IDs for what is being ordered (i.e., devices, plans, features, and/or accessories). These IDs can be obtained via the catalog-specific resources (i.e., **/catalog/devices**, **/catalog/plans**, **/catalog/accessories**).
  * Optional feature IDs for a specific plan can be obtained from the **/catalog/plans/{id}** resource.

* All required and optional order properties. Refer to the <a href="{{site.url}}/tutorials/properties">Obtaining Order Properties</a> page for steps how to identify the properties that are relevant to your order. Also, refer to this same page for instructions for <a href="{{site.url}}/tutorials/properties#choicePropertySubmittingOtherValue">submitting an "other/none of the above" value</a>.

* Shipping information. This is required for orders that include physical items. Refer to the <a href="{{site.url}}/tutorials/addresses">Formatting an Address</a> page for steps how to assemble the shipping components that are required to assemble a valid shipping address.


Here is an example of what the fully-assembled request body JSON might look like:

```
{
  "orderRequest": {
    "externalOrderNumber": "AZ99087547-001",
    "transactionType": "NEW_ACTIVATION",
    "regionId": "70144640",
    "postalCode": "06477",
    "shoppingCart": {
      "deviceId": "9194791812",
      "planId": "6568886494",
      "optionalFeatureIds": ["951", "88"],
      "accessoryIds": ["3628935899", "3628939022"]
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
            "text": "Please ship via FedEx"
          },
          {
            "id": "PREFERRED_AREA_CODE",
            "type": "TEXT",
            "text": "512"
          },
          {
            "id": "URGENCY",
            "type": "CHOICE",
            "choice": {
              "id": "2"
            }
          },
          {
            "id": "ORDER_REASON",
            "type": "CHOICE",
            "choice": {
              "id": "7",
              "otherText": "Current phone recalled for battery issue"
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
        "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/employees/28671599"
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
    "costSummary": [
      {
        "amount": 244.99,
        "currencyCode": "USD",
        "recurrence": "ONETIME"
      },
      {
        "amount": 538.69,
        "currencyCode": "USD",
        "recurrence": "MONTHLY"
      }
    ],
    "externalOrderNumber": "AZ99087547-001",
    "postalCode": "06477",
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
            "text": "Please ship via FedEx",
            "type": "TEXT"
          },
          {
            "id": "PREFERRED_AREA_CODE",
            "label": "Preferred Area Code",
            "text": "203",
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
    "region": {
      "_meta": {
        "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/regions/70144640"
      },
      "id": "70144640",
      "name": "United States"
    },
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
      "officePhone": "203-859-9999",
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
            "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/catalog/accessories/3628935899"
          },
          "id": "3628935899",
          "imageUrl": "https://cdn.tangoe.com/manage/images/devices/VER/MICRDUALVPCF.jpg",
          "manufacturer": {
            "_meta": {
              "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/Verizon%20Wireless"
            },
            "id": "Verizon Wireless",
            "logoUrl": "https://cdn.tangoe.com/manage/images/manufacturers/VerizonWireless.gif",
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
            "logoUrl": "https://cdn.tangoe.com/manage/images/carrier/logo_VERZN.gif",
            "name": "Verizon Wireless"
          }
        },
        {
          "_meta": {
            "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/catalog/accessories/3628939022"
          },
          "id": "3628939022",
          "imageUrl": "https://cdn.tangoe.com/manage/images/devices/VER/EMICUSBDTVL_F.jpg",
          "manufacturer": {
            "_meta": {
              "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/Verizon%20Wireless"
            },
            "id": "Verizon Wireless",
            "logoUrl": "https://cdn.tangoe.com/manage/images/manufacturers/VerizonWireless.gif",
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
            "logoUrl": "https://cdn.tangoe.com/manage/images/carrier/logo_VERZN.gif",
            "name": "Verizon Wireless"
          }
        }
      ],
      "device": {
        "_meta": {
          "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/catalog/devices/9194791812"
        },
        "id": "9194791812",
        "imageUrl": "https://cdn.tangoe.com/manage/images/devices/VER/sam_gal_s4.jpg",
        "manufacturer": {
          "_meta": {
            "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/Samsung"
          },
          "id": "Samsung",
          "logoUrl": "https://cdn.tangoe.com/manage/images/manufacturers/Samsung.gif",
          "name": "Samsung"
        },
        "name": "Samsung Galaxy S4 (16GB) - Black Mist",
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
          "logoUrl": "https://cdn.tangoe.com/manage/images/carrier/logo_VERZN.gif",
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
            "description": "Unlimited Push to Talk",
            "id": "951",
            "price": {
              "amount": 300.25,
              "currencyCode": "USD",
              "recurrence": "MONTHLY"
            }
          },
          {
            "description": "Extended Warranty",
            "id": "88",
            "price": {
              "amount": 200.44,
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
          "logoUrl": "https://cdn.tangoe.com/manage/images/carrier/logo_VERZN.gif",
          "name": "Verizon Wireless"
        }
      },
      "quantity": 1
    },
    "transactionType": "NEW_ACTIVATION"
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
      "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/orders/8710840"
    },
    "accountHolder": {
      "_meta": {
        "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/employees/28671599"
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
      "officePhone": "203-859-9999",
      "status": "ACTIVE"
    },
    "dateSubmitted": "2016-01-27T22:17:37Z",
    "externalOrderNumber": "AZ99087547-001",
    "orderId": "8710840",
    "orderSegments": {
      "items": [
        {
          "_meta": {
            "hrefHistory": "https://tngo-mobproc.cloudhub.io/mobproc/v1/orders/8710840/history?orderSegment=8710841",
            "hrefShipments": "https://tngo-mobproc.cloudhub.io/mobproc/v1/orders/8710840/shipments?orderSegment=8710841"
          },
          "accessories": [
            {
              "imageUrl": "https://cdn.tangoe.com/manage/images/devices/EMICUSBDTVL_F.jpg",
              "manufacturer": {
                "_meta": {
                  "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/Verizon%20Wireless"
                },
                "id": "Verizon Wireless",
                "logoUrl": "https://cdn.tangoe.com/manage/images/manufacturers/Verizon Wireless.gif",
                "name": "Verizon Wireless"
              },
              "name": "Verizon Wireless Rapid Wall Charger with 6ft Cable for Micro USB (EMICUSBDTVL-F)",
              "price": {
                "amount": 22.49,
                "currencyCode": "USD",
                "recurrence": "ONETIME"
              }
            },
            {
              "imageUrl": "https://cdn.tangoe.com/manage/images/devices/MICRDUALVPCF_m.jpg",
              "manufacturer": {
                "_meta": {
                  "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/Verizon%20Wireless"
                },
                "id": "Verizon Wireless",
                "logoUrl": "https://cdn.tangoe.com/manage/images/manufacturers/Verizon Wireless.gif",
                "name": "Verizon Wireless"
              },
              "name": "Verizon Wireless Vehicle Charger with Dual Output (MICRDUALVPC-F)",
              "price": {
                "amount": 22.49,
                "currencyCode": "USD",
                "recurrence": "ONETIME"
              }
            }
          ],
          "device": {
            "imageUrl": "https://cdn.tangoe.com/manage/images/devices/sam_gal_s4_blk.jpg",
            "manufacturer": {
              "_meta": {
                "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/manufacturers/Samsung"
              },
              "id": "Samsung",
              "logoUrl": "https://cdn.tangoe.com/manage/images/manufacturers/Samsung.gif",
              "name": "Samsung"
            },
            "name": "Samsung Galaxy S4 (16GB) - Black Mist",
            "price": {
              "amount": 199.99,
              "currencyCode": "USD",
              "recurrence": "ONETIME"
            }
          },
          "features": [],
          "orderSegmentId": "8710841",
          "plan": {
            "name": "Nationwide for Business Talk Share Plan",
            "optionalFeatures": [
              {
                "description": "Extended Warranty",
                "price": {
                  "amount": 200.44,
                  "currencyCode": "USD",
                  "recurrence": "MONTHLY"
                }
              },
              {
                "description": "Unlimited Push to Talk",
                "price": {
                  "amount": 300.25,
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
            "text": "2035559360",
            "type": "TEXT"
          },
          {
            "id": "ADDITIONAL_INSTRUCTIONS",
            "label": "Additional Instructions",
            "text": "Please ship via FedEx",
            "type": "TEXT"
          },
          {
            "id": "PREFERRED_AREA_CODE",
            "label": "Preferred Area Code",
            "text": "512",
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
        "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/employees/28671599"
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
      "officePhone": "203-859-9999",
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
    "status": "ORDER_SUBMITTED",
    "transactionType": "NEW_ACTIVATION"
  },
  "status": "SUCCESS"
}
  
```
<br/>


