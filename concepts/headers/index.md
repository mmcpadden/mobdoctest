---
layout: default
title: Request Headers 
youtubeId: zZfi3Vfy7lA
---


# Request Headers

Request headers are simply just key/value pairs of metadata relating to what is contained within the body of a request and/or response. The Mobile Procurement API primarily uses them for identifying users and limiting context. 

<br/>

## Video Tutorial

This video describes the custom headers that you can pass with any MOBPROC API request and how they are used.

{% include youtube.html id=page.youtubeId %}

We also recommend browsing all of our tutorials in our Video Library at [http://mobproc.tv](http://mobproc.tv).

<br />

## General Headers

General headers are added by the consumer of the API and are passed along to the appropriate backend system.

### X-TNGO-TENANT
Used to identify the specific tenant for which the API is being called (i.e., the customer).

* Required
* Data type: string

<br>

### client_id
Used to identify your client application as the one calling the API.

* Required
* Data type: string

<br />

## Context ID Headers

Frequently the logged-in/authenticated user calling the API is a service account. However, service accounts do not specifically identify the actual end user who is placing the order, viewing assets, etc. The true end user (also known as "the actor"), must be identified by setting a context header. The API will use these identifiers to set the context for making the API call. 

For example, imagine that an application called the API using a service account ID (i.e., admin.acme.com). Moreover, imagine that this request also included the X-TNGO-CONTEXT-COMPANYEMPLOYEEID header set to the company ID for an end user named Peter Edwards (i.e., pedwards.acme.com). When the request for the device catalog was executed, only devices that Peter Edwards was authorized to see would have been returned instead of the larger set of devices that the service account (admin.acme.com) was authorized to access.

<br>

### X-TNGO-CONTEXT-COMPANYEMPLOYEEID

This is the employee ID assigned by the tenant/customer (e.g., employee's email address, etc.). This header is only used when the X-TNGO-CONTEXT-EMPLOYEEID header is not set. If this header is NOT set, the context will be set to the authenticated user. 

* Optional
* Data type: string

<br>

### X-TNGO-CONTEXT-EMPLOYEEID 

This is the employee ID assigned by Tangoe. If this header is set, it will be used in place of the X-TNGO-CONTEXT-COMPANYEMPLOYEEID header. If it is NOT set, then the X-TNGO-CONTEXT-COMPANYEMPLOYEEID header will be used (if set).

* Optional
* Data type: string

<br>

### X-TNGO-CONTEXT-HIERARCHYID 

The Tangoe-assigned ID that is used to specify the organizational hierarchy to be used for the API call. If this header is not set, the default hierarchy will be used.

* Optional
* Data type: string


<br />

## Troubleshooting Headers

Troubleshooting headers are used to tag related and individual API calls, making it much easier to quickly locate their related activity in the server logs. All request and response activity that is logged for a single API call will be tagged with either/both of these headers, when they are set. Either header is useful, but they can be especially helpful when used together (with different values) to track a series of related API calls.

The  X-TNGO-CONTEXT-ACTIVITYID header can be used to tag multiple related calls with the same identifier. Different identifiers can be used for the X-TNGO-CONTEXT-CORRELATIONID header in each individual API call within this related set to help us distinguish between each individual call. Having this ability to group related activity, while simultaneously maintaining the ability to tag the individual calls that comprise your grouping, can be really helpful to troubleshoot an error occurring within a complex multi-step process.  

<br>

### X-TNGO-CONTEXT-ACTIVITYID

Set this header with a value of your choice for multiple related API calls in order to sort them into a logical grouping.

* Optional
* Data type: string

<br>

### X-TNGO-CONTEXT-CORRELATIONID

Set this header with a different unique value of your choice for each individual API call. 

* Optional
* Data type: string