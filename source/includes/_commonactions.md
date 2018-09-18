# Common Use Cases
```php
<?php
// --- Generic helper function to make CURL calls based on the protocol --- 

function jsonCurl($url, $json_data, $arr_params = NULL) {
	$ch = curl_init();
	curl_setopt($ch,CURLOPT_URL, $url);
	curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
	// Do not add POSTFIELDS for a GET request
	if (!($arr_params && array_key_exists('protocol', $arr_params) && $arr_params['protocol'] == 'get')) {
		curl_setopt($ch, CURLOPT_POSTFIELDS, $json_data);
	}
	if($arr_params && array_key_exists('header', $arr_params)) {
		curl_setopt($ch, CURLOPT_HTTPHEADER, $arr_params['header']);
	}
	else {
		curl_setopt($ch, CURLOPT_HTTPHEADER, array("Content-type: application/json"));
	}
	$response = curl_exec($ch);
	$error = curl_error($ch);
	$curl_http_code = curl_getinfo($ch, CURLINFO_HTTP_CODE);
	curl_close($ch);
	$arr_response = array();
	$arr_response['response'] = trim($response);
	$arr_response['error'] = trim($error);
	$arr_response['curl_http_code'] = $curl_http_code;
	return $arr_response;
}
?>
```
The following examples make use of the access token received in the [Authentication](#authentication) section above to interact with the server.

On the right, we have a helper function that makes the actual HTTP call to the Accellion server. It takes in the endpoint URL, input data in the form of JSON and HTTP parameters in an array.

This function will be used in the following code samples for different use cases.

## Listing folder contents
```php
<?php
define('CONST_EAPI_VERSION', 9);	// Put the latest API version in place of 9

$oAuthToken = "GET-OAUTH-TOKEN";	// Use the code in getOAuthToken.php to get the token

// --- Configuration Section ---
$folderId 			= 'YOUR-FOLDER-ID';
$kiteworks_hostname = 'YOUR-SERVER.DOMAIN.ORG';

$api_end_point = "https://$kiteworks_hostname/rest/folders/$folderId/children?deleted=false";
$headers = array("Content-Type: application/json",
			"Accept: application/json",
			"X-Accellion-Version: " . CONST_EAPI_VERSION,
			"Authorization: Bearer $oAuthToken" );

// --- Passing additional information about protocol and headers to the generic helper function --- 
$arr_params = [];
$arr_params['header'] = $headers;
$arr_params['protocol'] = 'get';

$postData = array();	//	API uses GET protocol. Does not require POST data. Initializing for the generic helper function
$arr_capi_response = jsonCurl($api_end_point, json_encode($postData), $arr_params);
print_r($arr_capi_response);
?>
```

In [Call an endpoint](#call-an-endpoint) section above, you called **GET /rest/users/me** to retrieve basic information about the current user (yourself).

One of the attributes returned was "syncdirId", which refers to your automatically created default "My Folder".

In the code sample on the right, replace "YOUR-FOLDER-ID" with the value of "syncdirId" (or the ID of any other folder you have access to). Also, replace "GET-OAUTH-TOKEN" with the access token retrieved in the [Authentication](#authentication) section, and "YOUR-SERVER.DOMAIN.ORG" with the hostname of your Accellion server.

Then, copy and paste the script on a machine that has PHP installed and run it. You will see the server response with the contents of your My Folder in JSON format.
