---
layout: default
title: Validate Access Token
---

# Access Tokens

## How to Validate an Access Token

The following information is required to validate an access token: 

* **access_token**: The access token to be validated.

<br>NOTE: Some client IDs may not have the permissions required to validate tokens. When requested, this permission is granted on a case by case basis.
<br>

### Endpoints 
An access token is validated by making an HTTP GET to our validation endpoint. The specific endpoint used will depend upon your instance's environment type:

* For **Production**: <code>http://tngo-prod-oauth-ping-validator.cloudhub.io</code>
* For **QA**: <code>http://tngo-qa-oauth-ping-validator.cloudhub.io</code>
 
<br>

### Request Query Parameters 
To validate an access token, the following query parameters need to be passed with this GET request: 
 
| ***Query Parameter*** | ***Description*** | ***Example*** |
| --- | --- | --- |
| **access_token** | The access token to be validated. | <code>jf8ANPs5ETFT1RPnfJnZpbcWjz2f</code> |

<br>


### Example 
The following is an example of a GET request to validate a token: 

```
https://oauthqa.tangoe.com/as/token.oauth2?grant_type=urn:pingidentity.com:oauth2:grant_type:validate_bearer&token=8jmFdZdww2h3FDYNJEHXgvtnXbdc&client_id=286ff94127c8801e9227d89337c14351&client_secret=72g30d2857fo4a41A9525345572AC72LK
```

The following is an example of a response that might be returned: 

```
 {
    "scope": "MOBPROC",
    "expires_in": 7186,
    "client_id": "286ff94127c8801e9227d89337c14351",
    "access_token": {
        "platform": "command",
        "tenant": "XYZ",
        "multitenant": "false",
        "domain": "*",
        "identityProvider": "pcv_edge",
        "identifier": "api.account.xyz"
    }
  }
```

The following properties are returned in the token validation response: 

| ***Property*** | ***Description*** | ***Example*** |
| --- | --- | --- |
| **scope** | OAuth scopes that are associated with the access token being validated. | <code>MOBPROC</code> |
| **expires_in** | Number of seconds that the access token will be valid. | <code>7188</code> |
| **client_id** | Tangoe-assigned client ID for the API client application that requested the access token being validated. | <code>a98b70cddd5f1432221360bd732f5ec1</code> |
| **platform** | Source system that is associated with the access token. | <code>command</code> |
| **tenant** | The Tangoe-assigned code that identifies the client account. | <code>ABC</code> |
| **multitenant** | Boolean to indicate that caller has access to data for more than one tenant. | <code>false</code> |
| **identityProvider** | Identifies the authenticating entity. | <code>pcv_edge</code> |
| **identifier** | Identity that was authorized by security provider (e.g., Ping). | <code>api.account.xyz</code> |


