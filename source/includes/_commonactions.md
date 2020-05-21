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

## List folder contents
```php
<?php
define('CONST_EAPI_VERSION', 10);	// Put the latest API version in place of 9

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

In [Call an endpoint](#call-an-endpoint) section above, you called **GET /rest/users/me** to retrieve basic information about the current user (yourself). One of the attributes returned was "syncdirId", which refers to your automatically created default "My Folder".

In this section, you will use the **GET /rest/folders/{folder ID}/children** endpoint to get the list of items (files and sub-folders) inside this folder.

In the code sample on the right, replace "YOUR-FOLDER-ID" with the value of "syncdirId" (or the ID of any other folder you have access to). Also, replace "GET-OAUTH-TOKEN" with the access token retrieved in the [Authentication](#authentication) section, and "YOUR-SERVER.DOMAIN.ORG" with the hostname of your Accellion server.

Then, copy and paste the script on a machine that has PHP installed and run it. You will see the server response with the contents of your My Folder in JSON format.

## Download file
```php
<?php
define('CONST_EAPI_VERSION', 10);    // Put the latest API version in place of 9

$oAuthToken = "GET-OAUTH-TOKEN";    // Use the code in getOAuthToken.php to get the token

// --- Configuration Section ---
$fileId           = 'YOUR-FILE-ID';
$kiteworks_hostname = 'YOUR-SERVER.DOMAIN.ORG';

$api_end_point = "https://$kiteworks_hostname/rest/files/$fileId/content";
$headers = array("Content-Type: application/json",
            "Accept: application/json",
            "X-Accellion-Version: " . CONST_EAPI_VERSION,
            "Authorization: Bearer $oAuthToken" );

// --- Passing additional information about protocol and headers to the generic helper function --- 
$arr_params = [];
$arr_params['header'] = $headers;
$arr_params['protocol'] = 'get';

$postData = array();    //  API uses GET protocol. Does not require POST data. Initializing for the generic helper function
$arr_capi_response = jsonCurl($api_end_point, json_encode($postData), $arr_params);
print_r($arr_capi_response);
?>
```

Next, let's use the **GET /rest/files/{file ID}/content** endpoint to get the contents of a file.

In the code sample on the right, replace "YOUR-FILE-ID" with one of the file IDs returned in the folder listing response (or the ID of any other file you have access to). Also, replace "GET-OAUTH-TOKEN" with the access token retrieved in the [Authentication](#authentication) section, and "YOUR-SERVER.DOMAIN.ORG" with the hostname of your Accellion server.

You will see the server response with the contents of your file in the 'response' key.

## Get current user Info
This example demonstrates how to get a current user’s information.
#### HTTP Method
GET
#### Request URL
https://{hostname}/rest/users/me
#### Request Body
None
#### Response
The response is a 200 OK status code. The response body contains a JSON representations of the user.
**Example Request**
```curl
'https://{hostname}/rest/users/me' \
      -H 'Accept: application/json' \
      -H 'Content-Type: application/json' \
      -H 'X-Accellion-Version: 15' \
      -H 'Authorization: Bearer {access_token}'
Example Response
{
    "active": true,
    "basedirId": 3,
    "created": "2019-06-28T02:20:12+0000",
    "deactivated": false,
    "deleted": false,
    "email": "user@email.com",
    "flags": 2,
    "id": 1,
    "internal": true,
    "mydirId": 4,
    "name": "User",
    "profileIcon": "1_e34981cfe2f471e31072fed529d83dfa395a9b540e7581d697f69f05935daaf3",
    "serviceName": "Service Name",
    "suspended": false,
    "syncdirId": 5,
    "userTypeId": 1,
    "verified": true
}
Example Python Code
import request

access_token = "ca648ba442acfd8fa8a57962bf7fc8233df0d87c"
api_version = "15
hostname = "hostname.com"

headers = {
    "Accept": "application/json",
    "X-Accellion-Version": api_version,
    "Authorization": "Bearer {access_token}".format(access_token=access_token)
}
url = "https://{hostname}/rest/users/me".format(hostname=hostname)
response = requests.get(url=url, headers=headers)
current_user = response.json()
```
