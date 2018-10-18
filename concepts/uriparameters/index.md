---
layout: default
title: URI Parameters
---

# Using URI Parameters to Obtain a Single Item

Many of the resources in this API return a collection of items (e.g., /assets/devices). However, where it makes sense, there are also many related resources that return a single item instead of the entire collection (/assets/devices/{id}). Often these calls for a single item include more detail data than what is provided within the collection response.

These calls for a single item are commonly performed by appending a *URI parameter* to a collection endpoint, with that  parameter being set to the ID of the specific item desired.

<br/>

## URI Parameters

A Uniform Resource Identifier (URI) is a string of characters used to identify a specific resource. The most common form of URI is the Uniform Resource Locator (URL).

The URI parameter is a parameter that is incorporated into the URL itself, rather than appended on the end of the URL (as commonly seen with query strings). For this API, the URI parameter is always set to the ID of the specific resource item that you wish to access. 

In the API documentation, you will see these URI parameters referenced as **"{id}"** within the resource signature (e.g., /catalog/devices/{id}).

```
 /catalog/devices/9194831760
```

