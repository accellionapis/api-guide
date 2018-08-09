# Authentication

## Signature Authorization flow

Accellion offers Signature Authorization flow for **trusted** apps where user interaction is impossible or undesirable. This is mostly applicable when some backend servers in your corporate network need to communicate with Accellion or when your app handles user authentication on its own.

<aside class="warning">
Apps using Signature Authorization flow can access any user's content, simply by specifying their email address. As such, these apps should only be accessible by trusted employees with high security clearance.
</aside>

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
2. Fetch access token from Accellion's token URI using the following parameters:
 * Client ID and secret: Displayed on Admin interface when app was created.
 * Grant Type: This should be the string “authorization_code” for the token request to work.
 * Scope: This is the scope of the API services that the client application wants to access. This should be a space-separated string that consists of the name of the services that the application requires. The requested scope must be a subset of the client application’s registered scope in the server.
 * Redirect URL: This is exactly the same redirect URI as registered with the server.
 * Code: This is the authorization code calculated in step one.

<aside class="notice">
Note that the step 2 is the same as OAuth 2.0 Authorization Code flow. Step 1 <b>calculates</b> the auth code instead of asking the user and Accellion server for it. This is what gives Signature Flow its power and risks.
</aside>

### Responses from server

Once these two steps are complete, if there are no errors for the POST request, the server will return a HTTP response 200 OK. The body for the response will be in JSON format and will include the following:

*	**access_token:** This is the token that will be used for all requests to the API.
*	**expires_in:** This is the number of seconds after which the access token will expire.
*	**scope:** This is the scope for which this token is valid.
*	**refresh_token:** This is a token that can be used to get a new access token without going through the first step of authorization.
*	**token_type:** This will be set to “bearer” because that is the type of token.


If there are problems with the request, the server will return a HTTP 400 bad request. The body of the response will contain error information in JSON format. Here are the possible values for the error code:

*	**invalid_client:** This indicates that the client application authentication failed. This is likely because either the client ID and/or the secret key provided are incorrect.
*	**invalid_grant:** This indicates that the user’s credentials are not valid.
*	**invalid_scope:** This indicates that the requested scope in invalid or exceeds the previously granted scope.
*	**invalid_request:** This indicates that the request is malformed, which usually means that there is a missing parameter that is required.
*	**unauthorized_client:** This indicates that the client application is not authorized to use this flow.
