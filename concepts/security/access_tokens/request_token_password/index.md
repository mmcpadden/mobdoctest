---
layout: default
title: Access Tokens
---

# Access Tokens


## How to Request an Access Token using the Password grant type

*NOTE: This tutorial assumes that you are using a client ID and client secret for an API client application that is configured to allow **Resource Owner Password Credentials** as one of its **Allowed Grant Types** configured in PingFederate.*

<br>

For server-to-server scenarios, it is appropriate to use the **password** grant type when getting an access token for use with Tangoe APIs. When using the **password** grant type, the following information is required: 

* **client_id**: Client ID assigned to your API client application.   
* **client_secret**: Client secret assigned to your API client application.
* **scope**: OAuth scope(s) required for the API you are using.
* **username**: Username for the Tangoe service account to be associated with the access token being created.
* **password**: Password for this service account. 

<br>

### Endpoints

An access token is requested by making an HTTP POST to our Ping authentication endpoint. The specific endpoint used will depend upon your instance's environment type:

* For **Production**: <code>https://oauth.tangoe.com/as/token.oauth2</code>
* For **QA**: <code>https://oauthqa.tangoe.com/as/token.oauth2</code>

*NOTE: Although a unique client ID/client secret pair can be used to access multiple APIs, they are always assigned per environment. Therefore, an client ID/secret pair that is valid for one environment will not work for another environment.*

<br>

### Request Query Parameters 

To get an access token, POST to the appropriate URL (above) for the type of environment your instance is using. The following query parameters need to be passed with this POST request: 

| ***Query Parameter*** | ***Description*** | ***Example*** |
| --- | --- | --- |
| **grant_type** | Always set to ''password''. | <code>password</code> |
| **client_id**  | Tangoe-assigned client ID for your specific API client application. | <code>376af94124f4400e9227c89937c12354</code> |
| **client_secret** | Tangoe-assigned client secret for your API client application. | <code>81f40d2777ea4a41A992535F17AC92EC</code> |
| **scope**         | OAuth scope that is required for the API being called. | <code>MOBPROC</code> |
| **username**      | Username for the Tangoe service account to be associated with the access token being created. This username must be formatted as follows: <code>&lt;source_system&gt;://&lt;username&gt;</code><br>The source system will be either: <code>command</code> or <code>rivermine</code><br>If the source system is a user on the Tangoe platform, then simply enter the username without prepending <code>&lt;source_system&gt;://</code> | <code>command://myusername</code> |
| **password** | Password associated with the username above. | <code>mypassword</code> |

<br>

### Example 

The following is an example of a POST request to get a token: 

```
 https://oauthqa.tangoe.com/as/token.oauth2?grant_type=password&client_id=376af94124f4400e9227c89937c12354&client_secret=81f40d2777ea4a41A992535F17AC92EC&scope=MOBPROC&username=command://myusername&password=mypassword 
```

The following is an example of a response that might be returned:

```
 { 
  "token_type": "Bearer", 
  "expires_in": 7199, 
  "refresh_token": "a9AhNvcffquYkV4bSw0O6gt4gKZRvTUGR2lfR8nJf4", 
  "access_token": "ts5grB07RB2GHAuOs67tJ4zJjV1R" 
 }
```

The following properties are returned in the access token response: 

| ***Property*** | ***Description*** | ***Example*** |
| --- | --- | --- |
| **token_type** | Our tokens will always be set to <code>Bearer</code>. | <code>Bearer</code> |
| **expires_in** | Number of seconds for which the access token will be valid. | <code>7199</code> |
| **access_token** | The actual access token to be used when making an API call. | <code>a9AhNvcffquYkV4bSw0O6gt4gKZRvTUGR2lfR8nJf4</code> |
| **refresh_token** | The refresh token used to refresh an existing access token. | <code>ts5grB07RB2GHAuOs67tJ4zJjV1R</code> |


