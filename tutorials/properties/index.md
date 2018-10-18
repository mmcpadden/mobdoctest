---
layout: default
title: Obtaining Order Properties
youtubeId: QBHmgTDW6-4
---


# Obtaining Order Properties

**This tutorial describes the steps for obtaining the required (and optional) properties that are supported by the vendor who will be fulfilling your order.**

Order properties are additional pieces of information about your order. They are not used by the API to process the order. But rather, they are passed on to the third-party vendor who will be fulfilling your order. Some of these properties may be required to be populated in order for your order to be accepted.

<br/>

## Video Tutorial

This video explains how to obtain the order properties that need to be passed when submitting any order request.

{% include youtube.html id=page.youtubeId %}

We also recommend browsing all of our tutorials in our Video Library at [http://mobproc.tv](http://mobproc.tv). 


<br/>

## The /orders/properties resource

An API call can be made to the **/orders/properties** resource to obtain the properties. The specific properties returned are determined based on the order transaction type and  vendor who will be fulfilling the order.

When making this API call, you must always set the **transactionType** query parameter (e.g., NEW_ACTIVATION, UPGRADE, SUSPEND, etc.). You also must also identify the vendor by passing either one of the two following query parameters:
 
* **vendor** -- The ID of the vendor who will be fulfilling the order. This ID can be obtained from many resources (e.g., /vendors, /catalog/plans, etc.).

* **service** -- The ID of the service asset for which the order is being executed, if applicable. The backend system will look up the ID of the vendor who is associated with the service asset.


Here is an illustration of what the order properties response could look like:

```
{
  "properties": {
    "_meta": {
      "href": "https://tngo-mobproc.cloudhub.io/mobproc/v1/orders/properties?transactionType=NEW_ACTIVATION&vendor=98"
    },
    "items": [
      {
        "fields": [
          {
            "id": "PREFERRED_AREA_CODE",
            "label": "Preferred Area Code",
            "maxLength": 3,
            "minLength": 3,
            "type": "TEXT",
            "validation": "REQUIRED"
          },
          {
            "id": "CONTACT_NUMBER",
            "label": "Contact Phone Number",
            "maxLength": 20,
            "minLength": 0,
            "text": "2038599360",
            "type": "TEXT",
            "validation": "REQUIRED"
          },
          {
            "id": "CONTACT_NUMBER_EXT",
            "label": "Ext",
            "maxLength": 10,
            "minLength": 0,
            "type": "TEXT",
            "validation": "OPTIONAL"
          },
          {
            "id": "ADDITIONAL_INSTRUCTIONS",
            "label": "Additional Instructions",
            "maxLength": 4000,
            "minLength": 0,
            "type": "TEXT",
            "validation": "OPTIONAL"
          },
          {
            "choices": [
              {
                "id": "19",
                "isOther": false,
                "label": "Broken Device"
              },
              {
                "id": "2",
                "isOther": false,
                "label": "Job required upgrade to data device"
              },
              ...
            ],
            "id": "Reason",
            "label": "Reason",
            "type": "CHOICE",
            "validation": "REQUIRED"
          }
        ],
        "groupId": "MISCELLANEOUS"
      }
    ]
  }
}
```

<br/>

## Contents of an Order Property

An individual order property contains the following required elements:

* **id** -- The unique ID identifying a specific order property.

* **label** -- A descriptive label that is suitable for displaying to an end user.

* **type** -- Indicates the type of data to which the value should be set:
  * *TEXT*: A string.
  * *DATE*: A date. (Note: the "pattern" element may be set to indicate the accepted format, e.g., ISO 8601, etc.)
  * *CHOICE*: An ID of a single option chosen from an array of choice options. 

* **validation** -- Indicates whether or not the property is required by the vendor.

They can also contain any of the following optional elements:

* **maxLength** -- The maximum number of characters to which this property can be set. 

* **minLength** -- The minimum number of characters to which this property must be set. 

* **pattern** -- A description of the pattern that the value should match (e.g, "mm/dd/yyyy"). This can be displayed to an end user as a hint describing what the properties value should look like.

* **regEx** -- A regular expression pattern that can be used to validate the value to which the property is set.


<a name="choiceProperty"></a><br/>

## The CHOICE Property Type

The CHOICE type is used to model properties that only accept a value from a defined list of options. An API consumer might use this property to surface a dropdown list in their user interface. 

CHOICE properties include an array named **choices** that contains the acceptable options. Each option in the array contains the following elements:

 * **id** -- The unique ID identifying this specific option.
 
 * **label** --  A descriptive label that is suitable for displaying to an end user.
 
 * **isOther** -- A boolean indicating if this specific option is used to select "other" (i.e., "none of the above"). There should only be one option in the array that has **isOther** set to true. Furthermore, this element can be helpful to the user interface for triggering another event, such as revealing a text field. This field can be used by an end user to describe a choice that is different from the ones found in a dropdown list.


<a name="choicePropertySubmittingOtherValue"></a><br/>

## CHOICE Properties: Submitting an "Other/None of the Above" Value

As mentioned above, a list of CHOICE properties (i.e., type = CHOICE) may include one option that allows your end user to indicate that they wish to select a response that is different than the ones currently offered. In other words, they wish to choose "none of the above." This "other/none of the above" option is indicated by the **isOther** property being set to true.

An example of this "other" choice in action would be if the consuming application presents a list of checkboxes for their end user to identify their current cellular carrier, with the final choice being "none of the above."  

However, simply choosing "none of the above" may be insufficient. The application may need to know what specific other choice the end user would prefer to submit. In this case, the user interface may want to reveal an input field for this when this "other/none of the above" option was checked. The value entered can then be passed via the "otherText" property as part of the POST to the **/orders** endpoint. 

The following is an example of what that portion of the POST request might look like:

```
{
  "orderRequest": {
    ...
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
            "id": "Carrier",
            "type": "CHOICE",
            "choice": {
              "id": "1",
              "otherText": "Allied Cellular"
            }
          }
        ]
      }
    ]
    ...
  }
}
```



