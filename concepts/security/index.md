---
layout: default
title: Authentication and Security
---

# Authentication

Users must be authenticated in order to use this API. This authentication is performed using the OAuth 2, the accepted industry standard for API security. Specifically, Tangoe currently supports the following two user credential OAuth grant types:

* Resource Owner Password Credentials
* Implicit

Below are descriptions of the uses cases for which each type is recommended. If you have a use case that you think would be better served by another grant type, please contact your Tangoe representative.

<br/>

## Grant Type: Resource Owner Password Credentials

This grant type requires a valid username and password that is sent with each API call. It is only recommended when the communication is server-to-server, that is when both sides of the transaction use an encrypted, private data store for these credentials. Generally speaking, in this use case there is no user interface controlling the API call into which a human can enter a password. An example of this would be a Tangoe client who subscribes to the Command platform and is calling this API directly from the Tangoe Mobility (ServiceNow) app using a service account. Please note that any service account used must be authorized to act up behalf of your logged-in end user.

<br/>

## Grant Type: Implicit

In cases where the end user needs to manually enter their password. For example, if a user is accessing the API directly from a device that does not have any secure storage (e.g., using the Developer Portal or an app on a Smartphone). For this case we offer the implicit grant type.

Please note that the when using the implicit grant type, your end user’s login credentials are not stored for the duration of the user’s session, only the token is stored. This allows for a trusted third-party to evaluate the credentials and authenticate the user without the hosting app have any access to those credentials. A popular example of this grant type in action would be an application that allows a user to authenticate with their Facebook username/password. The login popup is rovided by Facebook rather than the application itself and as a result the hosting application never has access to these credentials. In the case of Tangoe’s implicit OAuth 2, the Tangoe user store would act in the Facebook role.


<br/>

## Using the Access Token

Both grant types require a two-step process:

1.	A request must be made to a unique authentication URL to obtain an access token. Please contact your Tangoe representative for that URL.
1.	The consuming application must then pass that access token with every API call.

The **access token** for this API expires after one hour. However, when the access token is provided, Tangoe will also provide a **refresh token**. This refresh token can be passed with each call instead of the access token. Then, when the token is nearing expiration, a newly refreshed token will be automatically issued. In the case of the implicit grant type, this will save your end user the inconvenience of being repeatedly prompted to re-enter their username/password after an hour has passed. Refer to the [access token page]({{site.url}}/concepts/security/access_tokens/) for more information about how to [request a token]({{site.url}}/concepts/security/access_tokens/request-token-password/), [refresh a token]({{site.url}}/concepts/security/access_tokens/request-token-password/) <a href="./access-tokens/refresh-token/">refresh a token</a> or <a hef="./access-tokens/validate-token/">validate a token</a>.

Once you have obtained your access token, it will need to be passed in as a header with every request. Other required headers include your tenant code and your client ID. For example:

| ***Header Key*** | ***Example*** | ***Description*** |
| --- | --- | --- |
| **X-TNGO-TENANT**	| <code>XYZ</code> | Identifies the specific tenant for which the API is being called (i.e., the customer). |
| **client_id**	| <code>0123abc456def78ghij11bc213wxyz</code> | Identifies the specific client application as the one calling the API. |
| **Authorization**	| <code>Bearer <code>&lt;insert access token here&gt;</code> | Passed when using the Implicit authentication grant type. NOTE: The access token string must be preceded with the word “Bearer” |

<br/>
For more information regarding additional headers that may be passed, please refer to the <a href="/concepts/headers/">Request Headers</a> page.

<br/>


