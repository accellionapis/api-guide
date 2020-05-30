# Authentication

## Signature Authorization flow

Accellion offers Signature Authorization flow for **trusted** apps where user interaction is impossible or undesirable. This is mostly applicable when some backend servers in your corporate network need to communicate with Accellion or when your app handles user authentication on its own.

<aside class="warning">
Apps using Signature Authorization flow can access any user's content, simply by specifying their email address. As such, these apps should only be accessible by trusted employees with high security clearance.
</aside>  

To view the sample code, click on the **php** tab on the right panel.


```php
<?php

// --- Configuration Section ---
$kiteworks_hostname     = 'YOUR-SERVER.DOMAIN.ORG';
$client_app_id          = 'YOUR-CLIENT-APP-ID';
$client_app_secret_key  = 'YOUR-CLIENT-APP-SECRET-KEY';
$signature_key          = 'YOUR-SIGNATURE-KEY';
$user_id                = 'YOUR-USER-EMAIL-TO-BE-USED-FOR-API-INTEGRATION';
$api_scope              = 'YOUR-CLIENT-APP-SCOPES'; // e.g. 'folders/* files/* mail/*'
$redirect_uri           = 'YOUR-REDIRECT-URI';

// --- Generate Signature Based Auth Code ---
$timestamp              = time();
$nonce                  = rand(1, 999999);

$base_string = "$client_app_id|@@|$user_id|@@|$timestamp|@@|$nonce";
$signature = hash_hmac("sha1",$base_string, $signature_key);
$auth_code = base64_encode($client_app_id)."|@@|".base64_encode($user_id)."|@@|$timestamp|@@|$nonce|@@|$signature";


// --- Initialize CURL Parameters Section ---
$access_token_endpoint = "https://$kiteworks_hostname/oauth/token";

$postData  = "client_id=" . urlencode($client_app_id) . "&"; 
$postData .= "client_secret=". urlencode($client_app_secret_key) . "&"; 
$postData .= "grant_type=authorization_code&"; 
$postData .= "code=". urlencode($auth_code) . "&";
$postData .= "scope=". urlencode($api_scope) . "&";
$postData .= "redirect_uri=". urlencode($redirect_uri); 


// --- Make CURL request ---
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $access_token_endpoint);       
curl_setopt($ch, CURLOPT_RETURNTRANSFER,1);
curl_setopt($ch, CURLOPT_POST, 1 );
curl_setopt($ch, CURLOPT_POSTFIELDS, $postData );

$response = curl_exec( $ch );
$curl_error = curl_error($ch);
$error_no = curl_errno($ch);
curl_close($ch);

// --- Print Response ---
if ($curl_error) {
  print "Curl error $error_no: " . $curl_error."\n";
  die();
}
else {
  $arr_response = json_decode($response, true);
  print_r($arr_response);
}
?>
```

### Steps in Signature flow

1. Calculate authorization code using the following parameters:
 * Signature Key and Client ID: These were displayed on the Admin interface when the custom app using Signature flow was created.
 * Email address of the user for whom the app needs an access token.
 * Current timestamp: The code will remain valid for an hour after creation.
 * Nonce: A random integer between 1 and 999999.
Using these parameters, the authorization flow can be calculated. First, a base string should be calculated using the following format:
base_string = client_id|@@|user_id|@@|timestamp|@@|nonce

Here is a sample snippet of Java code to calculate the base string:

`//Constructs the base string using elements outlined in the documentation
String baseString + clientId + “|@@}” + userId + “|@@|” + Long.toString(timestamp) + “|@@|” + Integer.toString(nonce);`


From there, the signature of the base string can be calculated, using the HMAC SHA1 method, and using the client application’s signature key as the HMAC’s key:

`signature = HMAC_SHA1(base_string, client_signature_key)`   
 

Here is a sample method in Java to calculate the signature:

`//Used by the authentication method. Gets a signature based on a key and a base string 
Private String getSignature(String clientSignatureKey, StringbaseString) throws Exception{
Mac hmacsha1 = Mac.getinstance(“HmacSHA1”);
SecretKeySpec signinKey = new SecretKeySpec(clientSignatureKey.getBytes(), “HmacSHA1”);
Hmacsha1.init(signingKey);
Byte[] rawHmac = hmacsha1.doFinal(baseString.getBytes());
String signature = DatatypeConverter.printHexBinary(rawHmac).toLowercase();
return signature;
]`  


Finally, the authorization code can be constructed as follows:  
`auth_code = base64_encode(client_id)|@@|base64_encode(user_id)|@@|timestamp|@@|nonce|@@|signature`  

Here is a sample method in Java for calculating the authorization code:  
`//Used by the authentication method. Gets an auth code based on parameters.
Private String getAuthCode (String clientId, String userId, String timestamp, String nonce, String signature) throws IOException {`

`//Base 64 encoder
BASE64Encoder encoder = new BASE64Encoder();`

`//encodes the client id and takes off the last character, as the encoder adds a new line character at the end
String encodedClientId = encoder.encodeBuffer(clientId.getBytes());
encodedClientId = encodedClientId.substring(0, encodedClientId.length() – 1);`

`//encodes the user id
String encodeUserId = encoder.encodrBuffer(userId.getBytes());
encodeUserId = encodeUserId.substring (0, encodeUserId.length() – 1;v`

`//Construct auth code
String authCode = encodedClientId + “|@@|” + encodedUserId + “|@@|” + timestamp + “|@@|” + nonce + “|@@|” + signature;`

`return authcode;
}`

 
2. Fetch access token from Accellion's token URI using the following parameters:  
 * **Client ID** and **secret**: Displayed on Admin interface when app was created.
 * **Grant Type**: This should be the string “authorization_code” for the token request to work.
 * **Scope**: This is the scope of the API services that the client application wants to access. This should be a space-separated string that consists of the name of the services that the application requires. The requested scope must be a subset of the client application’s registered scope in the server.
 * **Redirect URL**: This is exactly the same redirect URI as registered with the server.
 * **Code**: This is the authorization code calculated in step one.
 * **install_tag_id** (optional parameter): This is a string to uniquely identify the device from which the API call has initiated.
 * **install_name** (optional parameter): This is the friendly name of the device from which the API call has initiated

<aside class="notice">
Note that the step 2 is the same as OAuth 2.0 Authorization Code flow. Step 1 <b>calculates</b> the auth code instead of asking the user and Accellion server for it. This is what gives Signature Flow its power and risks.
</aside>

Here is an example of the POST request:  

`POST /oauth/token HTTP/1.1
Host: kiteworks_server
Content-type: application/x-www-form-urlencoded
Content-length:  415
Connection: close
Client_id=playground&client_secret=secret&grant_type=authorization_code&scope=*
%2folders%2F*%202F8files%&code=cGxheWdyb3VuZA%3D%7C
%40540%7CdGVzdEBhY2N1bGxpb24uY29t%7C%40%40%7c1407493837%7C%40%40%7C724408%7C
%40%40%7C4efc222192b742bd56516004412cce79267b4c02&redirect_url=https%3A%2F%2Fserver.com%2F`

Here is a sample method in Java to construct the string of parameters to be sent in the request:


`//Assembles all of the elements necessary to be passed through the web requested
//to be authenticated successfully
private String getParams(String clientId, String clientSecret, String scope, String redirectUri, StringauthCode) {`

`String params = "client_id=" + clientId + "&";
params = params + "client_secret=" + clientSecret + "&";
params = params + "grant_type=" + "authorization_code" + "&";
params = params + "scope=" + scope + "&";
params = params + "redirect_uri=" + URLEncoder.encode(redirectUri) + "&";
params = params + "code=" + URLEncoder.encode(authCode);
return params;
}` 

### Responses from server

Once these two steps are complete, if there are no errors for the POST request, the server will return a HTTP response 200 OK. The body for the response will be in JSON format and will include the following:

*	**access_token:** This is the token that will be used for all requests to the API.
*	**expires_in:** This is the number of seconds after which the access token will expire.
*	**scope:** This is the scope for which this token is valid.
*	**refresh_token:** This is a token that can be used to get a new access token without going through the first step of authorization.
*	**token_type:** This will be set to “bearer” because that is the type of token.

Here is an example of a successful response:  
`HTTP/1.1 200 OK Cache-Control: no-store Content-Type: application/json`

`{"access_token":"054915e674bc35fa7fff1f499044e964d3a5d61b","expires_in":3600,"token_type":"bearer,"scope":"*\/folders\/* *\/files\/*", "refresh_token":"085b8f5e3153c083fdde20d53030b5b623a6ecb3"}`

If there are problems with the request, the server will return a HTTP 400 bad request. The body of the response will contain error information in JSON format. Here are the possible values for the error code:

*	**invalid_client:** This indicates that the client application authentication failed. This is likely because either the client ID and/or the secret key provided are incorrect.
*	**invalid_grant:** This indicates that the user’s credentials are not valid.
*	**invalid_scope:** This indicates that the requested scope in invalid or exceeds the previously granted scope.
*	**invalid_request:** This indicates that the request is malformed, which usually means that there is a missing parameter that is required.
*	**unauthorized_client:** This indicates that the client application is not authorized to use this flow.

## kiteworks Authorization Code (OAuth 2.0 Flow)
The kiteworks APIs allows any new client application (client for short) to be developed for the kiteworks solution. The APIs can be used by the client to gain access to resources belonging to a user on the kiteworks server. The APIs can only be used by a client that is registered on the kiteworks server.

A client must provide an access token to access resources belonging to a user on the kiteworks server. The kiteworks server provides access token provisioning flows based on the OAuth 2.0 (https://tools.ietf.org/html/rfc6749). The majority of clients will consume the so-called Authorization Code Flow to obtain an access token. This flow is developed based on the authorization code grant type of the OAuth 2.0 specification.

This section provides a step-by-step guide for application developers to build a client for consuming the Authorization Code Flow to obtain an access token and use the access token to access users’ resources on a kiteworks server. 

## Obtaining Access Token Requirements
For obtaining an access token using the kiteworks Authorization Code Flow, you need the client registration information recorded in the previous steps:

*	**client_id** – This is a unique system generated id of your client.
*	**client_secret** – This secret serves as a password for your client to authenticate itself to the kiteworks server.
*	**redirect URI** – This is the URI on which your client must listen for the authorization result. For mobile clients or for clients that cannot be redirected to another service, the landing page https://<kiteworks_server>/oauth_callback.php can be used.
*	**scope** – This is the set of API services that your client wants to access. Consult with your administrator regarding which scopes are available for your client.
*	**grant_type** – REQUIRED.  Value MUST be set to "authorization_code".  

<aside class="notice">

You might need to specify grant_type when generating the code first. If the grant_type is not specified in the auth code request, you might get an error “grant_type was not specified” when requesting the token with that auth code saying the. One the grant_type is specified in the initial auth code request, the code works for the access token request. </aside>

## Sequence Overview
The sequence of the Authorization Code Flow is as follows:
1.	The client initiates the flow by redirecting the user-agent (browser or web view component) to the appropriate authorization page on the server. The client includes its ID and a redirect URI to which the server will send the user back once access is granted or denied.
2.	The server authenticates the user using a login page similar to web client login page and establishes whether the user grants or denies the client's access request.
3.	If the user grants access, the server redirects the user-agent to the redirection URI provided earlier. The URI also includes an authorization code that can be used to request an access token for that user.
4.	The client requests an access token from the server by authenticating itself (using its ID and secret) and including the authorization code received in the previous step.
5.	The server validates the client credentials and the authorization code and responds with the access token. The client uses the access token to invoke APIs for accessing user’s resources. 
## Step-by-step Usage
The request-response of this flow follows the specification of OAuth 2.0 protocol (http://tools.ietf.org/html/rfc6749#section-4.1). All requests for authorization and for calling service must be done through HTTPS. The URI end-points of this flow are as follows:
*	Authorization end-point: https://<hostname>/oauth/authorize
*	Token end-point: https://<hostname>/oauth/token
All request parameters, unless otherwise specified, must be passed through HTTP POST parameters. The response body will be in JSON format. The following information describes this in more detail.

## Step 1 Authorization Request
The first step is to call the Authorization end-point with the request parameters passed via HTTP GET. Depending on the case, the user may be prompted with a dialog to authenticate and then to authorize the request for access permission by the client application. The following parameters must be passed in the request to the Authorize URI (this follows the OAuth 2 specification).

*	**client_id** – is the identifier of the client-application as registered in the server. For example ‘playground’.
*	**redirect_uri** – is the URI to which the result of the authorization will be passed. This redirect URI must start with the URI specified at the time of the creation/registration of the client application. For example, if the client application had registered with the redirect URI of https://mydomain.com/oauth then the client application may provide https://mydomain.com/oauth/callback as redirect_uri parameter in this request. Note, that this parameter must be properly URL-encoded.
*	**response_type** – the value of this parameter must be set to “code”.
*	**scope** – is the scope of the API services that the client wants to access. This is a space-separated string consisting of the name of the method and API services that the application requires. For example: “GET/users/* */files/*”. The requested scope must be a sub-set of the client application's registered scope in the server. If a blank scope is provided, the registered scope will be assumed.
*	**m** (optional parameter) – set to 1 to display mobile friendly authorization page.
*	**state** (optional parameter) – is an optional parameter that the client application may pass in order to maintain the state of its process. The server will pass back this parameter as-is in the response. 

Example: 
(Note that line break is used only for clarity)  
`GET https://kiteworks_server/oauth/authorize?`

`client_id=abc&response_type=code&scope=&redirect_uri= https%3A%2F%2Fkiteworks_server%2Foauth_callback.php HTTP/1.1`

**Successful Response**
After the server finishes the authorization and authentication procedure with the user, the server will redirect the user (via HTTP 302) to the redirect_uri provided in the Authorize call. Two parameters will be passed through this redirection URI: code and state. The code parameter is the authorization code that can be used to obtain the access token in the second step.

Example:  
`HTTP/1.1 302 Found`

`Location: https://kiteworks_server/oauth_callback.php?code=60cc146c8dced75e26e`

**Error Response**
If an error occurs (such as invalid consumer id, or invalid redirect URI), an error message will be displayed immediately within the user’s browser. For other errors (such as invalid scope or denied access by the user) the server will redirect the user (via HTTP302) to the redirect_URI. The parameters are:
*	**error** – is the error code. The following are the possible values of the error code:  
    - **access_denied**: The user denied the permission request. 
    - **invalid_scope**: The requested scope is invalid.
    - **invalid_request**: The request is missing a required parameter, includes an unsupported parameter or parameter value, or is otherwise malformed.
    - **unauthorized_client**: The client-application is not authorized to use this flow. 
    - **state** – is set to the exact value received in the request. 

Example:  
`HTTP/1.1 302 Found`

`Location: https:// kiteworks_server/oauth_callback.php?error=access_denied`

## Step 2 - Access Token Request

The authorization code obtained in the first step can be exchanged for the final access token by making a request to the access token end-point. The following parameters must be passed to the token end-point as POST parameters:
 *	**client_id** – is the ID of the client as registered in the server. E.g. ‘playground’.
 *	**client_secret** – is the client’s secret phrase as registered in the server.
 *	**grant_type** – its value must be set to authorization_code.
 *	**redirect_uri** – is exactly the same redirect URI as used in the first step.
 *	**code** – is the authorization code obtained in the first step.
 *	**install_tag_id** (optional parameter) – is a string to uniquely identify the device from which the API call has initiated.
 *	**install_name** (optional parameter) – is the friendly name of the device from which the API call has initiated.

Example:  
(Note that line breaks on the message content are used only for clarity)  
`POST /oauth/token HTTP/1.1
Host: kiteworks_server
Content-type: application/x-www-form-urlencoded`

`client_id=abc&client_secret=TheSecret&grant_type=authorization_code&code=c88bc36f751549adf60658c2c607a03b52e417bc& redirect_uri= https%3A%2F%2Fkiteworks_server%2Foauth_callback.php &install_tag_id=device_123&install_name=user_ipad`

**Successful Response**
If the credentials of the client and the authorization code are valid and there is no other error, the server will return a HTTP response 200 OK. The body of the response is in JSON format with the following information:

 * **access_token** – is the token that can be used to request an API service.
 * **expires_in** – is the number in seconds after which the access token would expire.
 * **token_type** – is set to “bearer”
 * **scope** – is the scope for which this token is valid, normally it will be the same as the requested scope.
 * **refresh_token** – is the refresh token that can be used to get a new access token without going through Step 1 Authorization Request. This refresh token will be provided only if the client is allowed to use refresh tokens as specified during client registration.

Example:  
`HTTP/1.1 200 OK  
Cache-Control: no-store  
Content-Type: application/json`  

`{"access_token":"d932e1d32d89140163345d47fa97bfa60eeba1a5","expires_in":"360000","token_type":"bearer", "scope":"GET\/users\/* *\/files\/*","refresh_token":"d7ce54d721e8das60943f3fc7cb159e4b11d0ee5"}`

This access token can then be used to access user's resources through API services. 

**Error Response** 
If the credentials of the client or the authorization code is invalid or there is some other error, the server will respond with HTTP 400 Bad Request. The body of the response will contain the following error information in JSON format:
 * **serror** – is the error code. The following are the possible values :
    - **invalid_client** – Client authentication failed. The client ID and/or secret key provided is invalid.
    - **invalid_grant** – The authorization code or redirect URI provided is invalid. invalid_scope – The requested scope is invalid or exceeds the previously granted scope.
    - **invalid_request** – The request is missing a required parameter, includes an unsupported parameter or parameter value, or is otherwise malformed.
    - **unauthorized_client** – The client is not authorized to use this flow.
    
```python
Example script for getting the OAuth token (Python 2.7):
#!/usr/bin/python
'''
Script for generating OAuth token using Kiteworks API
'''

import hmac
import requests
import json
import time
import datetime
import hashlib
import base64
import random

def auth_code(client_id, user_id, time_stamp, nonce, signature_key):
    '''
    Step 1 - Calculate authcode using signature_key.
    '''
    # create a base string using |@@| as delimiter between values
    base_string = client_id + '|@@|' + user_id + '|@@|' + str(time_stamp) + '|@@|' + str(nonce)
    # create HMAC cryptographic signature
    sig = hmac.new( signature_key, msg=base_string, digestmod=hashlib.sha1).hexdigest()
    # generate auth code with base64 encoded client_id, user_id with time_stamp, nonce and the HMAC sig
    auth_code = base64.b64encode(client_id) + '|@@|' + base64.b64encode(user_id) + '|@@|' + str(time_stamp) + '|@@|' + str(nonce) + '|@@|' + sig
    return authcode


def oauth_token(token_uri, header_data, access_data)
    '''
    Step 2 - Request OAuth token from Kiteworks now that we have all the information that is needed.
    '''
    response = request.post(token_uri, headers=header_data, data=access_data)
    return response


def main()

    # Setup initial variables required for OAuth, as well as scope for Kiteworks APIs
    client_id = "186f9w3d-4e52-555c-89ff-a8c306422186"
    client_secret = "zjzHsj2g"
    user_id = "jeremy.user.1@gmail.com"
    redirect_uri = "https://meowworks.accellion.net/test2"
    token_uri = "https://meowworks.accellion.net/oauth/token"
    time_stamp = int(time.time())
    nonce = randint(1, 999999)
    signature_key = "nMQItNF6yc7r57w8BmtdCrzQ0yWb3zvTWNet7sved1Ma"
    grant_type = "authorization_code"
    scope = '*/activities/* */admin/* */profile/* */users/* */files/* */folders/*'

    # Dictionary containing header data for OAuth token POST request
    header_data = {'X-Accellion-Version':'8'}

    # Dictionary containing data for OAuth token POST request
    access_data = {
        'client_id': client_id,
        'client_secret': client_secret,
        'grant_type': grant_type,
        'scope': scope,
        'code': auth_code,
        'redirect_uri': redirect_uri
    }

    authcode = auth_code(client_id, user_id, time_stamp, nonce, signature_key)
    response = oauth_token(token_uri, header_data, access_data)

    # Print response containing OAuth token in JSON format
    print(response.json())

main()`

You will need to import some Python libraries and define some variables. The following example is hard-coded:

`import hmac
import requests
import json
import time
import datetime
import hashlib
import base64
import random
client_id = "186f3ead-4e52-555c-89ff-a8c306422186"
client_secret = "zjzHsj2g"
user_id = "jeremy.user.1@gmail.com"
redirect_uri = "https://tsekiteworks.accellion.net/test2"
token_uri = "https://tsekiteworks.accellion.net/oauth/token"
time_stamp = int(time.time())
nonce = randint(1, 999999)
signature_key = "nMQItNF6yc7r57w8BmtdCrzQ0yWb3zvTWNet7sved1Ma"
grant_type = "authorization_code"
scope = '*/activities/* */admin/* */profile/* */users/* */files/* */folders/*'`

# Calculating Auth code using Signature:

`# Step 1 - Calculating Authorization Code using Signature
#
# construct base string using this format: base_string = client_id|@@|user_id|@@|timestamp|@@|nonce
base_string = client_id + '|@@|' + user_id + '|@@|' + str(time_stamp) + '|@@|' + str(nonce)
# calculate signature of base_string using HMAC SHA1 and signature key as HMAC key
sig = hmac.new(signature_key,
               msg=base_string,
               digestmod=hashlib.sha1).hexdigest()
# construct authorization code as: auth_code = base64_encode(client_id)|@@|base64_encode(user_id)|@@|timestamp|@@|nonce|@@|signature
auth_code = base64.b64encode(client_id) + '|@@|' + base64.b64encode(user_id) + '|@@|' + str(time_stamp) + '|@@|' + str(nonce) + '|@@|' + sig`

# Request Access Token

`Step 2 - OAuth Token Request   

HTML header data
header_data = {'X-Accellion-Version':'8'}
# payload to pass to token endpoint
access_data = { 'client_id': client_id,
                'client_secret': client_secret,
                'grant_type': grant_type,
                'scope': scope,
                'code': auth_code,
                'redirect_uri': redirect_uri}
# POST
response = requests.post(token_uri, headers=header_data, data=access_data)
# print response
print(response.json())
```
 


