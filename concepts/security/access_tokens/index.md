---
layout: default
title: Access Tokens
---

# Access Tokens

The MOBPROC REST API requires that users pass an access token with every API request.

An access token is obtained by making a request to a unique authentication URL. Please refer to the tutorial page entitled <a href="./request_token_password">How to **Request** an Access Token (for *Resource Owner Password Credentials* grant type)</a> for instructions how to do that.

The consuming application must then pass that access token with every API call. The access token for this API expires after one hour. However, when the access token is provided, Tangoe will also provide a refresh token. This refresh token can be passed with each call instead of the access token. Then, when the token is nearing expiration, a newly refreshed token will be automatically issued. In the case of the implicit grant type, this will save your end user the inconvenience of being repeatedly prompted to re-enter their username/password after an hour has passed. Please refer to the page entitled <a href="./refresh_token">How to **Refresh** an Access Token</a> for step-by-step instructions.

If you wish to determine whether your access token is currently valid and has not yet expired, please refer to the page entitled <a href="./validate_token">How to **Validate** an Access Token</a> for specific instructions.

<br>

## Tutorials for Managing Access Tokens 

* <a href="./request_token_password">How to **Request** an Access Token</a> (for *Resource Owner Password Credentials* grant type)
* <a href="./refresh_token">How to **Refresh** an Access Token</a>
* <a href="./validate_token">How to **Validate** an Access Token</a>

All of these tutorials tutorials are also available for download in one <a href="MOBROC_AccessTokens.pdf">convenient PDF file</a> (856 KB).

