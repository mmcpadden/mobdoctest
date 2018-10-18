---
layout: default
title: Getting Started  First call
---


# How to Make your First API Call in Just Minutes

Once you have registered your application to use this API, you will be provided with credentials needed to access the **Tangoe Developer Portal**, as well as what is needed for obtaining the access token that is required for calling the API (i.e., client ID, client secret, etc.). To log-in, click the **Developer Portal Log In** button in the upper right corner of this web site.

Upon logging into the Tangoe Developer Portal, simply click on the API Reference link in the navigation panel on the left (pictured below). The API Reference page provides extensive documentation describing each of the API resources, including all supported headers and query parameters. This reference guide also offers an interactive feature enabling you to submit requests for any resource. These requests will return real responses, populated with your own data.

<img src="{{site.url}}/images/screens/devportal.png" style="border:1px solid #666;" />

<br/>


## **Your API First Call**

After signing into the Tangoe Developer Portal, we recommend you begin by trying the */ping* resource to confirm your access to the backend services that power this API. The steps required to request this simple resource are essentially the same for all resources and can be used for exploring those as well.

To begin, simply follow these easy steps:

1. Click on the API Reference link in navigation panel on the left.
1. Scroll down to the Resources section and locate the **/ping** resource. 
1. Click on the **GET** tab to expand the panel that describes this resource.
1. Click on the **Try it** link, right below the GET tab. This will reveal the form used for testing this API call. 
1. In the AUTHENTICATION section, enter your **Client ID** and check the appropriate scope (i.e., **MOBPROC**). 
1. In the HEADERS section, enter your tenant header (i.e., **X-TNGO-TENANT**) and client ID (i.e., **client_id**).
1. In the QUERY PARAMETERS section, enter some text in the **echoText** parameter input. This text will be returned with a successful response.
1. Click the **GET** button to submit your request.


<img src="{{site.url}}/images/screens/first_call.jpg" style="border:1px solid #666;" />
<br/>

A successful response should be returned (i.e., HTTP status 200). The response body will include your echoText value, along with data identifying the backend system that processed your request. 

If a problem occurs while processing your request, you will receive an error message. Please refer to the <a href="{{site.url}}/concepts/errors/">Errors page</a> for an explanation of supported HTTP status codes and error messages.

<br />

