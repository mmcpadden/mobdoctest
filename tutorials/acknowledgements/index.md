---
layout: default
title: Obtaining Order Acknowledgements
---


# Obtaining Order Acknowledgements

**This tutorial describes the steps for obtaining the information and acknowledgements that the vendor requires to be disclosed to the user when placing an order to transfer ownership of a service asset.**


**Info blocks** are additional pieces of information that a vendor wishes to disclose when transferring ownership. Additionally, most vendors also require that the user placing the transfer order acknowledge one or more points before the order can be placed. These **acknowledgments** are not used by the API to process the order. But rather, they are passed on to the third-party vendor who will be fulfilling your order. Some of these acknowledgments may be required to be populated in order for your order to be accepted.

<br/>

## The /acknowledgements resource

An API call can be made to the **/acknowledgements** resource to obtain the acknowledgements. The specific acknowledgements returned are determined based on the order transaction type and vendor who will be fulfilling the order.

When making this API call, you must always set the following query parameters:
* **transactionType**  (e.g., XFER_SERVICE_OUT, XFER_SERVICE_IN, etc.). 
* **vendor** -- The ID of the vendor who will be fulfilling the order. This ID can be obtained from many resources (e.g., /vendors, /catalog/plans, etc.).

<br/>
Here is an illustration of an order acknowledgements request:

```
/acknowledgements?vendor=777&transactionType=XFER_SERVICE_IN
```

<br/>
The following example illustrates what the corresponding order acknowledgements response might look like:

```
{
    "infoblocks": [
    {
      "header": "Family & Shared Minute Plans",
      "paragraphs": [
        {
          "text": "PLEASE READ: In order to transfer a personally-liable account to a corporate-liable account, 
          the user may be required to have service with the corporate carrier currently."
        },
        {
          "text": "Before transferring your number, please contact your current carrier to resolve an outstanding 
          balance, if required."
        }
      ]
    },
    {
      "header": "Authorization of Transfer",
      "paragraphs": [
        {
          "text": "PLEASE READ: By transferring your number to the company, you agree to give up all rights to 
          that number. Depending upon the company's policy, the number may not be transferrable back to you."
        }
      ]
    },
    {
      "header": "Termination Fee Notice",
      "paragraphs": [
        {
          "text": "PLEASE READ: You may be required to pay a termination fee when moving your service number to a 
          new carrier. Please contact your cellular provider to determine if a termination fee will be charged."
        }
      ]
    }
  ],
  "acknowledgements": [
    {
      "id": "FAMILY_SHARED_MINUTE_PLAN",
      "label": "I verify that the service number to be transferred is no longer on a family shared plan. This has 
      been confirmed with my current carrier.",
      "type": "BOOLEAN",
      "validation": "REQUIRED"
    },
    {
      "id": "AUTHORIZATION_OF_TRANSFER",
      "label": "I authorize the transfer of liability for this service number to the company.",
      "type": "BOOLEAN",
      "validation": "REQUIRED"
    },
    {
      "id": "TERMINATION_FEES",
      "label": "I authorize this port of service to the new carrier. I accept personal responsibility for any 
      termination fees or other charges incurred.",
      "type": "BOOLEAN",
      "validation": "REQUIRED"
    }
  ]
}
```

<br/>

## Contents of an Info Block

An individual info block contains the following required elements:

* **header** -- A descriptive section heading label that is suitable for displaying to an end user.
* **paragraphs** -- An array of text blocks that relate to the corresponding header element. The text returned is suitable for displaying to an end user.

<br/>

## Contents of Acknowledgements

An individual acknowledgement contains the following required elements:

* **id** -- The unique ID identifying a specific acknowledgement.
* **label** -- A descriptive label describing the acknowledgement that is suitable for displaying to an end user.
* **type** -- Indicates the type of data to which the value should be set:
  * *BOOLEAN*: True or False. True indicates the user's acknowledgement. 
* **validation** -- Required or Optional. Indicates whether or not the property is required by the vendor.

