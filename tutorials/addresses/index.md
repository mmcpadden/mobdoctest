---
layout: default
title: Formatting an Address
youtubeId: psnb-pYO424
---


# How to Format an Address

**This tutorial describes the steps for obtaining the address components, in proper dispaly order, that are required for properly formatting an address for a selected country.**

When placing an order that requires the physical shipment of a device or accessory, shipping address information will need to be included in the request body. Moreover, different countries often require differing sets of components in order for an address to be valid for delivery to an address within that country.

In order to determine the required mailing address components for a selected country, an API call must be made to the **/regions/{id}** resource. Please note that this resource requires passing the ID of the selected country as a [URI parameter]({{site.url}}/concepts/uriparameters/). This call will return a response that includes a block called **addressFormat** that identifies all of the specific address components that are required. 

For example:

```
{
  "_meta": {
    "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/regions/70144640",
    "hrefChildren": "https://tngo-mobproc.cloudhub.io/mobproc/v1/regions?parentRegion=70144640"
  },
  "abbreviation": "US",
  "addressFormat": [
    {
      "id": "LINE_1",
      "label": "Line 1",
      "maxLength": 100,
      "minLength": 1,
      "validation": "REQUIRED"
    },
    {
      "id": "LINE_2",
      "label": "Line 2",
      "maxLength": 100,
      "minLength": 0,
      "validation": "OPTIONAL"
    },
    {
      "id": "LINE_3",
      "label": "Line 3",
      "maxLength": 100,
      "minLength": 0,
      "validation": "OPTIONAL"
    },
    {
      "id": "CITY",
      "label": "City",
      "maxLength": 50,
      "minLength": 1,
      "validation": "REQUIRED"
    },
    {
      "id": "STATE",
      "label": "State",
      "maxLength": 2,
      "minLength": 2,
      "options": [
        {
          "label": "Alabama",
          "value": "AL"
        },
        ...
        {
          "label": "Wyoming",
          "value": "WY"
        }
      ],
      "pattern": "[TX]",
      "regEx": "[A-Za-z][A-Za-z]",
      "validation": "REQUIRED"
    },
    {
      "id": "POSTAL_CODE",
      "label": "Postal Code",
      "maxLength": 10,
      "minLength": 4,
      "pattern": "[99999] or [99999-9999]",
      "regEx": "[0-9]{4,5}(-[0-9]{4})?",
      "validation": "REQUIRED"
    }
  ],
  "countryCode": "1",
  "currency": {
    "code": "USD",
    "name": "US Dollars",
    "symbol": "$"
  },
  "id": "70144640",
  "name": "United States",
  "postalCodePattern": "99999",
  "postalCodeRegEx": "^\\d{5}$",
  "type": "COUNTRY"
}
```


Once a selected country's address format is returned, the response can be parsed to assemble a **shipTo** block containing all of the required address components, along with the appropriate values. For example:

```
{
  "shipTo": {  
    "name": "Peter Edwards",
    "region": { 
      "id": "70144640"
    },
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

```


<br/>

## Video Tutorial

The following video explains how to assemble the shipping information for an order request. It includes instructions for determining the correct address format.

{% include youtube.html id=page.youtubeId %}

We also recommend browsing all of our tutorials in our Video Library at [http://mobproc.tv](http://mobproc.tv). 