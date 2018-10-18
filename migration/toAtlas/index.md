---
layout: default
title: Migration to Atlas
---


# Migration to the Atlas Platform (Beta)

There are some differences in how the Mobile Procurement API has been implemented for subscribers to Tangoe's Atlas Platform. Please be aware of these techncial differences. Some changes might be required in the consuming application's code when migrating your subscription to Atlas from another Tangoe backend platform (e.g., Premium Mobile/Command, Rivermine, etc.).

<br/>

## Code Changes that Might Be Required
### Device Features
Due to architectural reasons, the response returned from the Atlas platform for the **/assets/devices/{id}** endpoint includes this additional data block (i.e., <code>deviceFeatures</code>). Other Tangoe platforms require a unique catalog item for every device feature option combination (e.g., iPhone 7 32GB silver; iPhone 7 128GB gold, etc.). However, the Atlas platform supports multiple device feature option combinations for a generic device catalog item (e.g., iPhone 7). 

The following is an example of the device features block returned within the device asset detail response:
```
{
}
```

When submitting an order for a new device, the request must include an array of IDs for any selected features. For example:
```
{
}
```

<br/>

## Changes Not Requiring Code Changes 

### Service Plan Catalog Items
For the Atlas platform, the service plan catalog item detail response contains two distinct blocks for features: <code>includedFeatures</code> and <code>optionalFeatures</code>. For example: 
```
```

Please note that XXXXXXX


<br/>

### Catalog Item IDs (Device and Plan)
Please note that all IDs returned by the Mobile Procurement API are assigned by the API and can change. However, the structure of the ID can be different based on the Tangoe backend system to which you subscribe. For example, catalog item IDs for the Premium Mobile/Command platform are a continous string that is vendor-specific. However, IDs for the Atlas platform contain two concatenated segments separated with an underscore (e.g., XXXXXXX_XXXXXXXX). The first segment identifies the catalog item ID and the second segment identifies the vendor. 

<br/>

### Images (Data URLs)
For the Atlas platform, image values will be returned as data URLs. These URLs are prefixed with the <code>data:</code> scheme and allow content creators to embed small files inline into documents. Data URLs for images are supported on all modern browsers.

Data URLs are composed of four parts: a prefix (data:), a MIME type indicating the type of data, an optional base64 token if non-textual, and the data itself. For example:
```
 data:[<mediatype>][;base64],<data>
```
