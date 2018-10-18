---
layout: default
title: Access Tokens
---

# Access Tokens


## How to Refresh an Access Token

*NOTE: This tutorial assumes that you are using a client ID and client secret for an API client application that is configured to allow **Refresh Token** as one of its **Allowed Grant Types** configured in PingFederate.*

<br>

The following information is required to refresh an access token: 

* **client_id**: Client ID assigned to your API client application.   
* **client_secret**: Client secret assigned to your API client application.
* **scope**: OAuth scope(s) required for the API you are using.
* **refresh_token**: The refresh token that was obtained when the access token was created. 

<br>

### Endpoints 

An access token is refreshed by making an HTTP POST to our Ping authentication endpoint. The specific endpoint used will depend upon your instance's environment type:

* For **Production**: <code>https://oauth.tangoe.com/as/token.oauth2</code>
* For **QA**: <code>https://oauthqa.tangoe.com/as/token.oauth2</code>

*NOTE: Although a unique client ID/client secret pair can be used to access multiple APIs, they are always assigned per environment. Therefore, an client ID/secret pair that is valid for one environment will not work for another environment.*

<br>

### Request Query Parameters 

To refresh an access token, the following query parameters need to be passed with this POST request: 

| ***Query Parameter*** | ***Description*** | ***Example*** |
| --- | --- | --- |
| **grant_type** | Always set to *refresh_token*. | <code>refresh_token</code> |
| **client_id** | Tangoe-assigned client ID for your specific API client application. | <code>376af94124f4400e9227c89937c12354</code> |
| **client_secret** | Tangoe-assigned client secret for your API client application. | <code>81f40d2777ea4a41A992535F17AC92EC</code> |
| **scope** | OAuth scope that is required for the API being called. | <code>MOBPROC</code> |
| **refresh_token** | The refresh token that was associated with the access token that was previously created. | <code>a9AhNvcffquYkV4bSw0O6gt4gKZRvTUGR2lfR8nJf4</code> |

<br>

### Example 
The following is an example of a POST request to get a token: 

```
 https://oauthqa.tangoe.com/as/token.oauth2?grant_type=refresh_token&client_id=376af94124f4400e9227c89937c12354&client_secret=81f40d2777ea4a41A992535F17AC92EC&scope=MOBPROC&refresh_token=a9AhNvcffquYkV4bSw0O6gt4gKZRvTUGR2lfR8nJf4 
```

The following is an example of a response that might be returned:

```
 { 
   "token_type": "Bearer", 
   "expires_in": 7200, 
   "refresh_token": "wBzXpMnRjAyiLMDrn0vmhnO89pMbm22WJVXuf0RazH", 
   "access_token": "Ql0mwXlo8lWHmczeiopaDt5rLMTO" 
 } 
```

The following properties are returned in the newly-refreshed access token response: 

| ***Property*** | ***Description*** | ***Example*** |
| --- | --- | --- |
| **token_type** | Our tokens will always be set to <code>Bearer</code>. | <code>Bearer</code> |
| **expires_in** | Number of seconds for which the access token will be valid. | <code>7200</code> |
| **access_token** | The actual access token to be used when making an API call. | <code>wBzXpMnRjAyiLMDrn0vmhnO89pMbm22WJVXuf0RazH</code> |
| **refresh_token** | The refresh token used to refresh an existing access token. | <code>Ql0mwXlo8lWHmczeiopaDt5rLMTO</code> |


