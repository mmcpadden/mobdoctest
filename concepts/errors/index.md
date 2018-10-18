---
layout: default
title: Errors
youtubeId: 4WfcIxl0k5g
---

# Errors

This API returns a standard HTTP status code with every response to indicate whether your request had succeeded or failed. 

Status codes within the 200 range typically indicate that the request was processed successfully. Codes within the 400 range indicate an error resulting from the request data provided by the caller (e.g., a required parameter was missing from the request). And codes within the 500 range indicate an error triggered by an issue on Tangoe's servers. 

<br/>

## Video Tutorial

The following video explains how this API returns error responses to help you pinpoint your issue and get you back on track quickly.

{% include youtube.html id=page.youtubeId %}

We also recommend browsing all of our tutorials in our Video Library at [http://mobproc.tv](http://mobproc.tv). 

<br/>

## Supported HTTP Status Codes

The following status codes are currently supported:

| ***Status Code*** | ***Description*** |
| --- | --- |
| **200**	| *Success:* Your request was received, understood, and processed. 			|
| **400**	| *Bad Request:* Your request was missing required data or malformed. 	|
| **403**	| *Forbidden:* You are not authorized to receive what was requested. 		|
| **404**	| *Not Found:* The resource you requested could not be found.					|
| **500**	| *Server Error:* Your request failed due to a problem on Tangoe's servers.	|

<br/>
<br/>

## Error Messages

If an error does occur, the following error message information will be included within the response body:

* **status** -- One of the supported HTTP status codes listed above (e.g., 400).
* **id** -- Unique string identifying the specific occurrence of the error.
* **errorCode** -- Tangoe-assigned code indicating the type of error that occurred.
* **message** -- Description of the what caused the error and/or how to resolve it.


Here is an example of an error message for a missing (required) parameter:

```
{
  "errorCode": "400-Region_id-Account-Id-error",
  "id": "tghwn",
  "message": "Region ID or Service ID must be passed in.",
  "status": 400
}
```
