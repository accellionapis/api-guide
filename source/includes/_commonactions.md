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

To view the sample code, click on the **php** tab on the right panel. 

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
### HTTP Method  
GET  
### Request URL  
https://{hostname}/rest/users/me  
### Request Body  
None  
### Response  
The response is a `200 OK` status code. The response body contains a JSON representations of the user.  

### Example Request 
To view the **Example Request** sample code, click on the **cURL** tab on the right panel.  

```shell
https://{hostname}/rest/users/me' \
      -H 'Accept: application/json' \
      -H 'Content-Type: application/json' \
      -H 'X-Accellion-Version: 15' \
      -H 'Authorization: Bearer {access_token}
```  
  
  
  
  
  
  
    
  
### Example Response  
To view the **Example Response** sample code, click on the **json** tab on the right panel.  

```json
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
```  

### Example Python Code  
To view the **Example Python Code** sample code, click on the **Python** tab on the right panel.  

```python
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

# Manage Folders
This section gives examples for the following:  
  
*	Get top level folders  
*	Create a Folder  
*	Get a particular folder    

## Get top level folders
This example demonstrates how to get a list of top level folders sorted by ascending order of the folder name.
### HTTP Method
GET
### Request URL
https://{hostname}/rest/folders/top
### Request Body
None
### Response
The response is a `200 OK` status code. The response body contains an array of JSON representations of the folder.  

**Example Request**  
To view the **Example Request** sample code, click on the **cURL** tab on the right panel. 

```shell
https://{hostname}/rest/folders/top?orderBy=name%3Aasc' \
      -H 'Accept: application/json' \
      -H 'Content-Type: application/json' \
      -H 'X-Accellion-Version: 15' \
      -H 'Authorization: Bearer {access_token}'
```  

**Example Response**  
To view the **Example Response** sample code, click on the **json** tab on the right panel. 

```json
{
    "data": [
        {
            "id": 100,
            "created": "2020-05-06T04:36:09+0000",
            "deleted": false,
            "permDeleted": false,
            "expire": 0,
            "modified": "2020-05-14T07:35:29+0000",
            "name": "FolderA",
            "parentId": 3,
            "userId": 1,
            "type": "d",
            "permalink": "https:\/\/{hostname}\/w\/o8FOt1ycGVL8MEIN",
            "description": "",
            "syncable": false,
            "fileLifetime": 0,
            "secure": false,
            "path": "FolderA"
        },
        {
            "id": 5,
            "created": "2019-06-28T02:20:12+0000",
            "deleted": false,
            "permDeleted": false,
            "expire": 0,
            "modified": "2019-08-22T07:04:40+0000",
            "name": "My Folder",
            "parentId": 3,
            "userId": 1,
            "type": "d",
            "permalink": "https:\/\/{hostname}\/w\/JKncWKCQrIURunW9",
            "description": "",
            "syncable": true,
            "fileLifetime": 0,
            "secure": false,
            "path": "My Folder"
        }
    ],
    "metadata": {
        "total": 2,
        "limit": null,
        "offset": 0
    }
}
```  

**Example Python Code**  
To view the **Example Python Code** sample code, click on the **Python** tab on the right panel.  

```python
import requests

headers = {
    "Accept": "application/json",
    "X-Accellion-Version": api_version,
    "Authorization": "Bearer {access_token}".format(access_token=access_token)
}
url = "https://{hostname}/rest/folders/top?orderBy=name%3Aasc".format(hostname=hostname)
response = requests.get(url=url, headers=headers)
response_json = response.json()
folders = response_json["data"]
```  

## Create a Folder
This example demonstrates how to create a new top level folder named "MyNewFolder".  `{folder_id} = 0` means the folder is created at the user's top level.
### HTTP Method
POST
### Request URL
https://{hostname}/rest/folders/{folder_id}/folders
### Request Body
`{
    "name": "MyNewFolder"
}'
### Response
If the folder is created successfully, the response is a `201 Created` status code. 
The response header `X-Accellion-Location` contains the URI that you can use for subsequent requests.
If `returnEntity=true` is specified in the query string, the response body contains a JSON representation of the data source, including a `id` property that you can use as the data source ID for subsequent requests.

**Example Request**  
To view the **Example Request** sample code, click on the **cURL** tab on the right panel.   

```shell
   -X POST 'https://{hostname}/rest/folders/0/folders?returnEntity=true' \
   -H 'Accept: application/json' \
   -H 'Content-Type: application/json' \
   -H 'X-Accellion-Version: 15' \
   -H 'Authorization: Bearer {access_token}' \
   -d '{"name": "MyNewFolder"}'
```  

**Example Response**  
To view the **Example Response** sample code, click on the **json** tab on the right panel.   

```json
{
    "id": 1234,
    "created": "2020-05-15T05:33:19+0000",
    "deleted": false,
    "permDeleted": false,
    "expire": 0,
    "modified": "2020-05-15T05:33:19+0000",
    "name": "MyNewFolder",
    "parentId": 3,
    "userId": 1,
    "permalink": "https:\/\/{hostname}\/w\/DD9J7QUIz1kj6ZPR",
    "type": "d",
    "description": null,
    "syncable": false,
    "fileLifetime": 0,
    "secure": false,
    "path": "MyNewFolder",
    "maxFolderExpiration": 0,
    "maxFileLifetime": 0
}
```  

**Example Python Code**  
To view the **Example Python Code** sample code, click on the **Python** tab on the right panel.    

```python
import requests

headers = {
    "Accept": "application/json",
    "X-Accellion-Version": api_version,
    "Authorization": "Bearer {access_token}".format(access_token=access_token)
}
url = "https://{hostname}/rest/folders/{folder_id}/folders?returnEntity=true".format(
    hostname=hostname, folder_id=folder_id
)
data = {
    "name": folder_name
}
response = requests.post(url=url, headers=headers, data=data)
folder = response.json()
```  

## Get a particular folder
This example demonstrates how to retrieve the folder (MyNewFolder) that you created in the previous example. When you create a new data source, the `dataStreamId` includes a unique identifier (shown as "1234" in these examples). Be sure to use the id from the data source you created.
### HTTP Method
GET
### Request URL
https://{hostname}/rest/folders/{folder_id}
### Request Body
None
### Response
If the data source exists, the response is a `200 OK` status code. The response body contains a JSON representation of the data source.  

**Example Request**  
To view the **Example Request** sample code, click on the **cURL** tab on the right panel.   

```shell
   -X POST 'https://{hostname}/rest/folders/0/folders?returnEntity=true' \
   -H 'Accept: application/json' \
   -H 'Content-Type: application/json' \
   -H 'X-Accellion-Version: 15' \
   -H 'Authorization: Bearer {access_token}' \
   -d '{"name": "MyNewFolder"}'
```  

**Example Response**  
To view the **Example Response** sample code, click on the **json** tab on the right panel.   

```json
{
    "id": 1234,
    "created": "2020-05-15T05:33:19+0000",
    "deleted": false,
    "permDeleted": false,
    "expire": 0,
    "modified": "2020-05-15T05:33:19+0000",
    "name": "MyNewFolder",
    "parentId": 3,
    "userId": 1,
    "permalink": "https:\/\/{hostname}\/w\/DD9J7QUIz1kj6ZPR",
    "type": "d",
    "description": null,
    "syncable": false,
    "fileLifetime": 0,
    "secure": false,
    "path": "MyNewFolder",
    "maxFolderExpiration": 0,
    "maxFileLifetime": 0
}
```  

**Example Python Code**  

To view the **Example Python Code** sample code, click on the **Python** tab on the right panel.    

```python
import requests

headers = {
    "Accept": "application/json",
    "X-Accellion-Version": api_version,
    "Authorization": "Bearer {access_token}".format(access_token=access_token)
}
url = "https://{hostname}/rest/folders/{folder_id}/folders?returnEntity=true".format(
    hostname=hostname, folder_id=folder_id
)
data = {
    "name": folder_name
}
response = requests.post(url=url, headers=headers, data=data)
folder = response.json()
```

# Manage Files
This section gives examples for the following:
Upload a file

* Initiate multi-chunks upload
* Chunk upload
* Download a file

## Upload a file
This example demonstrates how to upload a new file named "MyNewFile.txt" to a previously created folder. 
You must initiate a multi-chunks Upload before you can upload any part. In response of your initiated request, the API will return an upload URI that you must include in the upload as part of the request.
In this example, a file 'MyNewFile.txt' will be uploaded in 2 chunks with 1KB in each chunk (the user can choose their own chunk size).  
## Initiate multi-chunks upload
### HTTP Method
POST
### Request URL
https://{hostname}/rest/folders/{folder_id}/actions/initiateUpload
### Request Body
filename - The new file name
totalSize - the total size of the file
totalChunks - the total number of chunks
`{
    "filename": "MyNewFile.txt",
    "totalSize": 2048,
    "totalChunks": 2
}`
### Response
If the upload is initiated successfully, the response is a `201 Created` status code. 
The response body contains a JSON representation of the chunk upload info, including a `uri` property (dacfs_upload1/rest/uploads/7890 in this sample request) that you can use as the upload URI for subsequent requests.

**Example Request**  

To view the **Example Request** sample code, click on the **cURL** tab on the right panel.   

```shell
   -X POST 'https://{hostname}/rest/folders/1234/actions/initiateUpload' \
   -H 'Accept: application/json' \
   -H 'Content-Type: application/json' \
   -H 'X-Accellion-Version: 15' \
   -H 'Authorization: Bearer {access_token}' \
   -d '{"filename": "MyNewFile.txt", "totalSize": 2048, totalChunks': 2}'
```  

**Example Response**  

To view the **Example Response** sample code, click on the **json** tab on the right panel.   

```json
{
    "error": "OK",
    "totalSize": 2048,
    "timestamp": "2020-05-15T08:11:07Z",
    "uri": "dacfs_upload1/rest/uploads/7890",
    "userId": 1,
    "lastTimestamp": "2020-05-15T08:11:07Z",
    "uploadedSize": 0,
    "clientName": "OAuth Playground",
    "fileUrl": "",
    "location": "{hostname}",
    "totalChunks": 2,
    "uploadedChunks": 0,
    "completeOk": 0,
    "svrUploadTime": 0,
    "id": 7890,
    "replaceId": null,
    "backend": "acfs"
}
```  

**Example Python Code**  

To view the **Example Python Code** sample code, click on the **Python** tab on the right panel.    

```python
import requests
import os

headers = {
    "Accept": "application/json",
    "X-Accellion-Version": api_version,
    "Authorization": "Bearer {access_token}".format(access_token=access_token)
}

To initiate Upload
file_size = os.path.getsize(upload_file_path)
file_name = os.path.basename(upload_file_path)

To calculate the number of chunks to be uploaded in sequence
total_num_of_chunks = file_size // chunk_size

For e.g. 7 // 2 = 3. But the actual number of chunks should be 4 [2, 2, 2, 1]
if file_size % chunk_size:
    total_num_of_chunks += 1

For min number of chunks is 0 even though file size is 0
if total_num_of_chunks == 0:
    total_num_of_chunks = 1

payload = {"filename": file_name, "totalSize": file_size, "totalChunks": total_num_of_chunks}
url = "https://{hostname}/rest/folders/{folder_id}/actions/initiateUpload".format(
    hostname=hostname,
    folder_id=folder_id
)
response = requests.post(url=url, data=payload, headers=headers)
response_json = response.json()

To get upload uri
upload_uri = response_json["uri"]
```

## Chunk upload
### HTTP Method
POST
### Request URL
https://{hostname}/{upload_uri}
### Request Body
compressMode - The compression mode of the chunk (only NORMAL is supported).
compressionSize - The compressed size (it’s always the same as originalSize).
originalSize - The original size of the chunk.
index - The index of the file.
`{
    "compressionMode": "NORMAL",
    "compressionSize": 1024,
    "originalSize": 1024,
    "index": 1
}`
### Response
If the individual chunk is completed, the response is a `200 OK` status code.  Once the whole multi-chunks upload is completed successfully, the response is a `201 Created` status code.
The response body contains a JSON representation of the chunk upload info, including a uri property (dacfs_upload1/rest/uploads/7890 in this sample request) that you can use as the URI for subsequent requests.  

**Example Request**
Please note that the chunk /tmp/MyNewFile.txt_chunk_1 was generated beforehand not the actual full file.  
To view the **Example Request** sample code, click on the **cURL** tab on the right panel.   

```shell
   -X POST 'https://{hostname}/dacfs_upload1/rest/uploads/7890?returnEntity=true' \
   -H 'Accept: application/json' \
   -H 'X-Accellion-Version: 15' \
   -H 'Authorization: Bearer {access_token}' \
   -F 'compressionMode=NORMAL' \
   -F 'compressionSize=1024' \
   -F 'originalSize=1024' \
   -F 'index=1' \
   -F 'content=@/tmp/MyNewFile.txt_chunk_1'
```  

**Example Response (chunk)**  

To view the **Example Response** sample code, click on the **json** tab on the right panel.   

```json
{
    "error": "OK",
    "totalSize": 2048,
    "timestamp": "2020-05-15T08:11:07Z",
    "uri": "dacfs_upload1/rest/uploads/7890",
    "userId": 1,
    "lastTimestamp": "2020-05-15T08:11:07Z",
    "uploadedSize": 1024,
    "clientName": "OAuth Playground",
    "fileUrl": "",
    "location": "{hostname}",
    "totalChunks": 2,
    "uploadedChunks": 1,
    "completeOk": 0,
    "svrUploadTime": 0,
    "id": 7890,
    "replaceId": null,
    "backend": "acfs"
}
```  

**Example Response (Final chunk)**  

To view the **Example Response** sample code, click on the **json** tab on the right panel.   

```json
{
    "locked": false,
    "description": "",
    "created": "2020-05-15T06:25:16Z",
    "deleted": false,
    "clientModified": null,
    "fingerprint": "Generating...",
    "userId": 1,
    "modified": "2020-05-15T09:30:14Z",
    "clientCreated": null,
    "name": "MyNewFile.txt",
    "overriddenExpire": false,
    "expire": null,
    "mime": "text/plain",
    "permDeleted": false,
    "parentId": 1234,
    "type": "f",
    "id": 1240,
    "size": 2048
}
```  

**Example Python Code**  

To view the **Example Python Code** sample code, click on the **Python** tab on the right panel.    

```python
class FileLimiter(object):

    def __init__(self, file_obj, read_limit):
        self.read_limit = read_limit
        self.file_obj = file_obj

    def read(self):
        data = self.file_obj.read(self.read_limit)
        return data

# get upload uri
upload_url = response_json["uri"]

file_obj = None
with open(upload_file_path, "rb") as fn:
    url = "https://{hostname}/{upload_url}?returnEntity=true".format(hostname=hostname, upload_url=upload_url)

    # upload the files in chunks
    for index in range(1, total_num_of_chunks + 1):
        read_limit = chunk_size if index < total_num_of_chunks else (file_size - (chunk_size * (index - 1)))
        offset = (index - 1) * chunk_size
        fn.seek(offset)

        # reading file in chunks
        payload = {"compressionMode": "NORMAL", "compressionSize": read_limit, "originalSize": read_limit, "index": index}
        response = requests.post(url=url, files={"content": FileLimiter(fn, read_limit)}, data=payload, headers=headers)
        if index == total_num_of_chunks:
            file_obj = response.json()

return file_obj
```  

**Example whole multi-chunks**  

To view the **Example Python Code** sample code, click on the **Python** tab on the right panel.    

```python
import requests
import os

class FileLimiter(object):

    def __init__(self, file_obj, read_limit):
        self.read_limit = read_limit
        self.file_obj = file_obj

    def read(self):
        data = self.file_obj.read(self.read_limit)
        return data

headers = {
    "Accept": "application/json",
    "X-Accellion-Version": api_version,
    "Authorization": "Bearer {access_token}".format(access_token=access_token)
}

To initiate Upload
file_size = os.path.getsize(upload_file_path)
file_name = os.path.basename(upload_file_path)

To calculate the number of chunks to be uploaded in sequence
total_num_of_chunks = file_size // chunk_size

For e.g. 7 // 2 = 3. But the actual number of chunks should be 4 [2, 2, 2, 1]
if file_size % chunk_size:
    total_num_of_chunks += 1

For min number of chunks is 0 even though file size is 0
if total_num_of_chunks == 0:
    total_num_of_chunks = 1

payload = {"filename": file_name, "totalSize": file_size, "totalChunks": total_num_of_chunks}
url = "https://{hostname}/rest/folders/{folder_id}/actions/initiateUpload".format(
    hostname=hostname,
    folder_id=folder_id
)
response = requests.post(url=url, data=payload, headers=headers)
response_json = response.json()
# get upload uri
upload_url = response_json["uri"]

file_obj = None
with open(upload_file_path, "rb") as fn:
    url = "https://{hostname}/{upload_url}?returnEntity=true".format(hostname=hostname, upload_url=upload_url)

    # upload the files in chunks
    for index in range(1, total_num_of_chunks + 1):
        read_limit = chunk_size if index < total_num_of_chunks else (file_size - (chunk_size * (index - 1)))
        offset = (index - 1) * chunk_size
        fn.seek(offset)

        # reading file in chunks
        payload = {"compressionMode": "NORMAL", "compressionSize": read_limit, "originalSize": read_limit, "index": index}
        response = requests.post(url=url, files={"content": FileLimiter(fn, read_limit)}, data=payload, headers=headers)
        if index == total_num_of_chunks:
            file_obj = response.json()
return file_obj
```
# Manage Mail
This section gives examples for the following:  

* Send a mail with files
*	Send mail  

## Send a Mail with Files
This example demonstrates how to send a mail with an uploaded file to "user1@email.com", "user2@email.com".  

## Send mail  

### HTTP Method  

POST
### Request URL  
https://{hostname}/rest/mail/actions/sendFile
### Request Body  
draft - 1 means create a draft mail. 0 - send the mail directly
`{
    "to": ["user1@email.com", "user2@email.com"],
    "cc": [],
    "bcc": [],
    "files": [1240],
    "subject": "Test mail subject",
    "body": "Test mail body",
    "draft": 0
}`  
### Response
If the upload is initiated successfully, the response is a `201 Created` status code.
The response body contains a JSON representation of the mail, including a id property (256 in this sample request) that you can use as the mail ID for subsequent requests.  

**Example Request**  

To view the **Example Request** sample code, click on the **cURL** tab on the right panel.   

```shell
   -X POST 'https://{hostname}/rest/mail/actions/sendFile?returnEntity=true' \
   -H 'Accept: application/json' \
   -H 'Content-Type: application/json' \
   -H 'X-Accellion-Version: 15' \
   -H 'Authorization: Bearer {access_token}' \
   -d '{"to": ["user1@email.com", "user2@email.com"], "files": [1240], "subject": "Test mail subject", "body": "Test mail body", "draft": 0}'
```  

**Example Response**  

To view the **Example Response** sample code, click on the **json** tab on the right panel.   

```json
{
    "emailPackageId": 197,
    "isRead": false,
    "variables": [
        {
            "variable": "HOST",
            "value": "{hostname}"
        },
        {
            "variable": "SENDER_EMAIL",
            "value": "{user@email.com}"
        },
        {
            "variable": "FILE_COUNT",
            "value": 1
        },
        {
            "variable": "EXPIRATION_DATE",
            "value": "Aug 28, 2020"
        },
        {
            "variable": "DOWNLOAD_LINK",
            "value": "https://{hostname}/w/Vo0FUmZryMDQVpsiGnEmDdd9F86kklnpyUNfQlNPCRe3m"
        },
        {
            "variable": "REGISTRATION_LINK",
            "value": "https://{hostname}/register?w=Vo0FUmZryMDQVpsiGnEmDdd9F86kklnpyUNfQlNPCRe3m"
        },
        {
            "variable": "SUBJECT",
            "value": "Test mail subject"
        },
        {
            "variable": "BODY",
            "value": "Test mail body"
        },
        {
            "variable": "FILES_LIST",
            "value": "\n                <table border=\"0\" cellpadding=\"0\" cellspacing=\"0\" width=\"100%\" style=\"min-width:100%; border-collapse:collapse\"><tbody>\n                    <tr>\n         <td valign=\"top\" style=\"padding:20px 0; border-top:1px solid #f2f2f2\">\n            \n                <table border=\"0\" cellpadding=\"0\" cellspacing=\"0\" width=\"100%\" style=\"min-width:100%;border-collapse:collapse;\"><tbody>\n                    <tr>\n        <td style=\"padding: 0 14px 0 20px;width: 1%;\">\n  <img alt=\"audio\" width=\"24\" height=\"24\" src=\"https://{hostname}styles/img/email-template/document.png\">\n                        </td>\n                        <td style=\"font-size: 16px;line-height: 1.25;color:#000000\"><span>1 audio file</span></td>\n      </tr>\n                    <tr>\n                        <td></td>\n                        <td style=\"font-size: 14px;color: #404040;line-height: 1.5;padding-right:20px;\"><span>MyNewFile.txt</span></td>\n     </tr>\n      </tbody></table>\n            \n                        </td>\n     </tr>\n       </tbody></table>\n            "
        }
    ],
    "secureBody": false,
    "error": null,
    "senderId": 1,
    "templateId": 19,
    "id": 256,
    "date": "2020-05-20T07:25:15Z",
    "bucket": "outgoing",
    "isUserSent": true,
    "type": "original",
    "parentEmailId": null,
    "emailReturnReceipt": [],
    "isPreview": false,
    "recipients": [
        {
            "isDistributionList": false,
            "email": "user1@email.com",
            "type": 0,
            "userId": 1
        },
        {
            "isDistributionList": false,
            "email": "user2@email.com",
            "type": 0,
            "userId": 2
        }
    ],
    "subject": "Test mail subject",
    "deleted": false,
    "status": "queued",
    "watermark": null
}
```  

**Example Python Code**  

To view the **Example Python Code** sample code, click on the **Python** tab on the right panel.    

```python
import requests
 
access_token = "ca648ba442acfd8fa8a57962bf7fc8233df0d87c"
api_version = "15
hostname = "hostname.com"
 
headers = {
    "Accept": "application/json",
    "X-Accellion-Version": api_version,
    "Authorization": "Bearer {access_token}".format(access_token=access_token)
}
 
url = "https://{hostname}/rest/mail/actions/sendFile?returnEntity=true".format(
    hostname=hostname
)
data = {
    "to": ["user1@email.com", "user2@email.com"],
    "cc": [],
    "bcc": [],
    "files": [250],
    "subject": "Test mail subject",
    "body": "Test mail body",
    "draft": 0
}
response = requests.post(url=url, headers=headers, data=data)
mail = response.json()
```


