# Sample Code in JavaScript
## Download a File  
```javascript
//----------------------------------------------------------------------------------------------------------------------------------------------------------------
// Download a file:
//----------------------------------------------------------------------------------------------------------------------------------------------------------------
function (fileId, filename, code) {
  return new Promise((resolve, reject)=>{
    console.log("GET:"+fileId);
    request.get({url: SERVER_URL + "/rest/files/" + fileId + "/content",
      headers: {"X-Accellion-Version": API_VERSION}},(error, response, body) => {
        return resolve(response);
    }).auth(null, null, true, TOKEN).pipe(fs.createWriteStream(filename));
  });
};
```

## Upload a File  
```javascript
//----------------------------------------------------------------------------------------------------------------------------------------------------------------
// Upload a file:
//----------------------------------------------------------------------------------------------------------------------------------------------------------------
function uploadFile (fileId, filename, code) {
  return new Promise((resolve, reject)=>{
  console.log("MULTIPART:"+fileId);
    request.post({url: SERVER_URL+ "/rest/folders/" + fileId + "/actions/file?returnEntity=true",
      headers: {"X-Accellion-Version": API_VERSION,"Accept": "application/json"},
      formData: {file: fs.createReadStream(filename)}},(error, response, body) => {
        return resolve(response);
    }).auth(null, null, true, TOKEN);
  });
};
```
 
## Send Secure Mail  
```javascript
//----------------------------------------------------------------------------------------------------------------------------------------------------------------
// Send secure mail:
//----------------------------------------------------------------------------------------------------------------------------------------------------------------
var email = {
  to:["to1@test1.com","to1@test2.com"],       // The TO field
  cc:["cc1@test1.com","cc2@test2.com"],       // The CC field
  bcc:["bcc1@test1.com","bcc2@test2.com"],    // The bcc field
  files:[101,338,4445],                       // The file ID's on Accellion to attach
  draft: false,                               // Save as draft
  watermark: "Confidential",                  // The watermark text
  secureBody: true,                           // Secure email body
  selfCopy: true,                             // Send copy to yourself
  includeFingerprint: true,                   // Include hashes for the attached files
  body: "Hello World",                        // The email body
  subject: "Here are some files",             // The email subject
};

function sendMail (email) {
  return new Promise((resolve, reject)=>{
    request.post({url: SERVER_URL+ "/mail/actions/sendFile?returnEntity=true",
      headers: {"X-Accellion-Version": API_VERSION,"Accept": "application/json"},
      form: email,(error, response, body) => {
        return resolve(response);
    }).auth(null, null, true, TOKEN);
  });
}
```
 
## Invite a User to a Folder  
```javascript
//----------------------------------------------------------------------------------------------------------------------------------------------------------------
// Invite user to a folder:
//----------------------------------------------------------------------------------------------------------------------------------------------------------------
/**
* @param {Number} id The id of the folder
* @param {Array} emails The email addresses to add
* @param {boolean} notify Send notification email
* @param {Number} role The role id to assign to the users
* @param {boolean} notifyFile Send notification on file additions
*/
function inviteUsersToFolder (id, emails, notify, role, notifyFile) {
  var item = {notify: notify, emails: emails, roleId: role, notifyFileAdded: notifyFile};
  request.post({url: SERVER_URL+ "/folders/" + id + "/members?returnEntity=true&mode=full&updateIfExists=true&partialSuccess=true",
    headers: {"X-Accellion-Version": API_VERSION,"Accept": "application/json"},
    form: item,(error, response, body) => {
      return resolve(response);
  }).auth(null, null, true, TOKEN);
}
```
