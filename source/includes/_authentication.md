# Authentication

## kiteworks Authorization Code (OAuth 2.0 Flow)

The kiteworks APIs allows any new client application (client for short) to be developed for the kiteworks solution. The APIs can be used by the client to gain access to resources belonging to a user on the kiteworks server. The APIs can only be used by a client that is registered on the kiteworks server.

A client must provide an access token to access resources belonging to a user on the kiteworks server. The kiteworks server provides access token provisioning flows based on the OAuth 2.0 (https://tools.ietf.org/html/rfc6749). The majority of clients will consume the so-called Authorization Code Flow to obtain an access token. This flow is developed based on the authorization code grant type of the OAuth 2.0 specification.

This document provides a step-by-step guide for application developers to build a client for consuming the Authorization Code Flow to obtain an access token and use the access token to access users’ resources on a kiteworks server. Example codes for Android based clients are also provided

### Obtaining Access Token Requirements

For obtaining an access token using the kiteworks Authorization Code Flow, you need the client registration information recorded in the previous steps:

*	**client_id** – This is a unique system generated id of your client.
*	**client_secret** – This secret serves as a password for your client to authenticate itself to the kiteworks server.
*	**redirect URI** – This is the URI on which your client must listen for the authorization result. For mobile clients or for clients that cannot be redirected to another service, the landing page https://<kiteworks_server>/oauth_callback.php can be used.
*	 **scope** – This is the set of API services that your client wants to access. Consult with your administrator regarding which scopes are available for your client.

### Sequence Overview

The sequence of the Authorization Code Flow is as follows:

Step 1.	The client initiates the flow by redirecting the user-agent (browser or web view component) to the appropriate authorization page on the server. The client includes its ID and a redirect URI to which the server will send the user back once access is granted or denied.

Step 2.	The server authenticates the user using a login page similar to web client login page and establishes whether the user grants or denies the client's access request.

Step 3.	If the user grants access, the server redirects the user-agent to the redirection URI provided earlier. The URI also includes an authorization code that can be used to request an access token for that user.

Step 4.	The client requests an access token from the server by authenticating itself (using its ID and secret) and including the authorization code received in the previous step.

Step 5.	The server validates the client credentials and the authorization code and responds with the access token. The client uses the access token to invoke APIs for accessing user’s resources.

### Step-by-step Usage

The request-response of this flow follows the specification of OAuth 2.0 protocol (http://tools.ietf.org/html/rfc6749#section-4.1). All requests for authorization and for calling service must be done through HTTPS. The URI end-points of this flow are as follows:

*	Authorization end-point: https://<hostname>/oauth/authorize  
*	Token end-point: https://<hostname>/oauth/token
  
All request parameters, unless otherwise specified, must be passed through HTTP POST parameters. The response body will be in JSON format. The following information describes this in more detail.

### Step 1 Authorization Request

The first step is to call the Authorization end-point with the request parameters passed via HTTP GET. Depending on the case, the user may be prompted with a dialog to authenticate and then to authorize the request for access permission by the client application. The following parameters must be passed in the request to the Authorize URI (this follows the OAuth 2 specification).

*	**client_id** – is the identifier of the client-application as registered in the server. For example ‘playground’.
*	**redirect_uri** – is the URI to which the result of the authorization will be passed. This redirect URI must start with the URI specified at the time of the creation/registration of the client application. For example, if the client application had registered with the redirect URI of https://mydomain.com/oauth then the client application may provide https://mydomain.com/oauth/callback as redirect_uri parameter in this request. Note, that this parameter must be properly URL-encoded.
*	**response_type** – the value of this parameter must be set to “code”.
*	**scope** – is the scope of the API services that the client wants to access. This is a space-separated string consisting of the name of the method and API services that the application requires. For example: “GET/users/  /files/”. The requested scope must be a sub-set of the client application's registered scope in the server. If a blank scope is provided, the registered scope will be assumed.
*	**m** (optional parameter) – set to 1 to display mobile friendly authorization page.
*	**state** (optional parameter) – is an optional parameter that the client application may pass in order to maintain the state of its process. The server will pass back this parameter as-is in the response. 

Code Example on the right:
(Note that line break is used only for clarity)

> GET https://kiteworks_server/oauth/authorize?

> client_id=abc&response_type=code&scope=&redirect_uri= https%3A%2F%2Fkiteworks_server%2Foauth_callback.php HTTP/1.1 

<br><br>

<br><br>


**Successful Response**

After the server finishes the authorization and authentication procedure with the user, the server will redirect the user (via HTTP 302) to the redirect_uri provided in the Authorize call. Two parameters will be passed through this redirection URI: code and state. The code parameter is the authorization code that can be used to obtain the access token in the second step.

Code Example on the right:

> HTTP/1.1 302 Found 

> Location: https://kiteworks_server/oauth_callback.php?code=60cc146c8dced75e26e 

<br><br>

<br><br>


**Error Response**

If an error occurs (such as invalid consumer id, or invalid redirect URI), an error message will be displayed immediately within the user’s browser. For other errors (such as invalid scope or denied access by the user) the server will redirect the user (via HTTP302) to the redirect_URI. The parameters are:

•	error – is the error code. The following are the possible values of the error code:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; o	**access_denied:** The user denied the permission request.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; o	**invalid_scope:** The requested scope is invalid.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; o	**invalid_request:** The request is missing a required parameter, includes an unsupported parameter or parameter value, or is otherwise malformed.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; o	**unauthorized_client:** The client-application is not authorized to use this flow.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; o	**state** – is set to the exact

Code Example on the right:

> HTTP/1.1 302 Found 

> Location: https:// kiteworks_server/oauth_callback.php?error=access_denied 

<br><br>

<br><br>


### Step 2 - Access Token Request

The authorization code obtained in the first step can be exchanged for the final access token by making a request to the access token end-point. The following parameters must be passed to the token end-point as POST parameters:

*	**client_id** – is the ID of the client as registered in the server. E.g. ‘playground’.
*	**client_secret** – is the client’s secret phrase as registered in the server.
*	**grant_type** – its value must be set to authorization_code.
*	**redirect_uri** – is exactly the same redirect URI as used in the first step.
*	**code** – is the authorization code obtained in the first step.
*	**install_tag_id** (optional parameter) – is a string to uniquely identify the device from which the API call has initiated.
*	**install_name** (optional parameter) – is the friendly name of the device from which the API call has initiated.


Code Example on the right: (Note that line breaks on the message content are used only for clarity)

> POST /oauth/token HTTP/1.1

> Host: kiteworks_server

> Content-type: application/x-www-form-urlencoded

> client_id=abc&client_secret=TheSecret&grant_type=authorization_code&code=c88bc36f751549adf60658c2c607a03b52e417bc& redirect_uri= https%3A%2F%2Fkiteworks_server%2Foauth_callback.php &install_tag_id=device_123&install_name=user_ipad 

<br><br>

<br><br>

<br><br>

<br><br>


**Successful Response**

If the credentials of the client and the authorization code are valid and there is no other error, the server will return a HTTP response 200 OK. The body of the response is in JSON format with the following information:

*	**access_token** – is the token that can be used to request an API service.
*	**expires_in** – is the number in seconds after which the access token would expire.
*	**token_type** – is set to “bearer”.
*	**scope** – is the scope for which this token is valid, normally it will be the same as the requested scope.
*	**refresh_token** – is the refresh token that can be used to get a new access token without going through Step 1 Authorization Request. This refresh token will be provided only if the client is allowed to use refresh tokens as specified during client registration.

Code Example on the right:

> HTTP/1.1 200 OK

> Cache-Control: no-store

> Content-Type: application/json

> {"access_token":"d932e1d32d89140163345d47fa97bfa60eeba1a5","expires_in":"360000","token_type":"bearer", "scope":"GET\/users\/* *\/files\/*","refresh_token":"d7ce54d721e8das60943f3fc7cb159e4b11d0ee5"}


<br><br>

<br><br>

<br><br>

<br><br>

This access token can then be used to access user's resources through API services. 

**Error Response**

If the credentials of the client or the authorization code is invalid or there is some other error, the server will respond with HTTP 400 Bad Request. The body of the response will contain the following error information in JSON format:

*	**error** – is the error code. The following are the possible values:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; o	**invalid_client** – Client authentication failed. The client ID and/or secret key provided is invalid.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; o	**invalid_grant** – The authorization code or redirect URI provided is invalid. invalid_scope – The requested scope is invalid or exceeds the previously granted scope.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; o	**invalid_request** – The request is missing a required parameter, includes an unsupported parameter or parameter value, or is otherwise malformed.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; o	**unauthorized_client** – The client is not authorized to use this flow. 


## Signature Authorization flow
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
Accellion offers Signature Authorization flow for **trusted** apps where user interaction is impossible or undesirable. This is mostly applicable when some backend servers in your corporate network need to communicate with Accellion or when your app handles user authentication on its own.

<aside class="warning">
Apps using Signature Authorization flow can access any user's content, simply by specifying their email address. As such, these apps should only be accessible by trusted employees with high security clearance.
</aside>

### Steps in Signature flow
There are two main steps to performing the signature flow. The first is to calculate the authorization flow, and the second is to request an access token. These two steps are described in detail below:

### Calculate the Authorization code

The following parameters are required to calculate the authorization code:

*	**client_id:** This is the client application’s ID, which is registered in the server and was given when the client application was created in the admin UI.
*	**client_signature_key:** This is the client application’s signature key. This is also registered in the server and was given when the client application was created.
*	**user_id:** This is the user’s id (either the email address or the integer id associated with the user). This is required for the client application to access the appropriate resources.
*	**timestamp:** This is the timestamp at the time (in UTC) that this signature is generated. The signature code will only remain valid within an hour of creation.
*	**nonce:** A random integer between 1 and 999999.


From there, the signature of the base string can be calculated, using the HMAC SHA1 method, and using the client application’s signature key as the HMAC’s key:


Here is a sample method in Java to calculate the signature:

Finally, the authorization code can be constructed as follows:

Here is a sample method in Java for calculating the authorization code:


### Access Token Request

Make a POST request to the token endpoint with the following parameters:

*	**client_id:** This is the client application’s ID, which is registered in the server and was given when the client application was created in the admin UI.
*	**client_secret:** This is the client application’s secret phrase, which is registered in the server and was given when the client application was created in the admin UI.
*	**grant_type:** This should be set the string “authorization code” for the token request to work.
*	**scope:** This is the scope of the API services that the client application wants to access. This should be a space-separated string that consists of the name of the services that the application requires. The requested scope must be a subset of the client application’s registered scope in the server.
*	**redirect_url:** This is exactly the same redirect URI as registered with the server.
*	**code:** This is the authorization code calculated in step one.
*	**install_tag_id** (optional parameter): This is a string to uniquely identify the device from which the API call has initiated.
*	**install_name** (optional parameter): This is the friendly name of the device from which the API call has initiated.

Here is an example of the POST request:


Here is a sample method in Java to construct the string of parameters to be sent in the request:


Once these two steps are complete, if there are no errors for the POST request, the server will return a HTTP response 200 OK. The body for the response will be in JSON format and will include the following:

*	**access_token:** This is the token that will be used for all requests to the API.
*	**expires_in:** This is the number of seconds after which the access token will expire.
*	**scope:** This is the scope for which this token is valid.
*	**refresh_token:** This is a token that can be used to get a new access token without going through the first step of authorization.
*	**token_type:** This will be set to “bearer” because that is the type of token.
 
Here is an example of a successful response:


If there are problems with the request, the server will return a HTTP 400 bad request. The body of the response will contain error information in JSON format. Here are the possible values for the error code:

*	**invalid_client:** This indicates that the client application authentication failed. This is likely because either the client ID and/or the secret key provided are incorrect.
*	**invalid_grant:** This indicates that the user’s credentials are not valid.
*	**invalid_scope:** This indicates that the requested scope in invalid or exceeds the previously granted scope.
*	**invalid_request:** This indicates that the request is malformed, which usually means that there is a missing parameter that is required.
*	**unauthorized_client:** This indicates that the client application is not authorized to use this flow.
