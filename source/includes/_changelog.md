# Changelog

## Version 9.1 (August 2018, Server Version 5.1)

### New Endpoints

1. GET /rest/admin/mail - Use this new admin endpoint to get metadata for emails on the system. You can specify parameters like sender, date and status to get a subset of all the emails.

### Deprecated endpoints

1. Bulk read/unread is available under the endpoints PATCH /mail/actions/read|unread, so the following endpoints have been deprecated:
 * PATCH /mail/{emailId}/actions/read
 * PATCH /mail/{emailId}/actions/unread

### Removed endpoints

1. POST /requestFile/{ref}/actions/sendUploadNotification - removed unsupported endpoint.

### Changed Endpoints

1. POST /folders/{id}/members - If you try to add a folder member with a recipient profile, error code ERR_PROFILE_COLLABORATION_DISABLED will be returned now instead of ERR_ENTITY_IS_RECIPIENT.
2. GET /activities - Activities about emails that you received - 'received_preview', 'received_mail' and 'received_file_request' - have been removed from the response. You will still see activities about the emails that you sent.
3. POST /profiles - Spaces are not allowed in the profile name.
4. GET /files/actions/zip - Only .zip format can be downloaded (.tar is not supported anymore).
5. All admin endpoints - When accessing an admin endpoint with non-admin user, code is changed from ERR_ACCESS_APPLICATION_ADMIN to ERR_ACCESS_ADMIN. When accessing a dli endpoint, code is changed from ERR_ACCESS_DLI_ADMIN to ERR_ACCESS_ADMIN (when accessed by a non-admin user). Accessing dli endpoints with other admins (non-DLI admins) will get ERR_ACCESS_USER.
6. POST /admin/users - If the user already exists, 'failedIds' and 'field' will not be returned anymore. ERR_ENTITY_EXISTS will still be returned as before.
7. Some file related endpoints - 'location' field will not be returned for the following endpoints:
 * GET /files/{id}/versions
 * GET /files/{id}/versions/id
 * GET /mail/{mailId}/attachments?with=(frozenFile)
 * GET /mail/{mailId}/attachments/{attachmentId}?with=(frozenFile)
8. GET /mail/{id} - added 'markAsRead' query param, set to false by default. This param is added to give clients more flexibility when reading unread emails.
9. Some mobile sync endpoints - Added fileDlpStatus, fileAvStatus fields to the response of following endpoints:
 * GET /mobileSyncItems
 * GET /mobileSyncItems/{id}
10. Some DLI endpoints: In response body "types" array property has been replaced by "type" string property, because each Export belongs to only one type.
 * GET /rest/dli/exports
 * GET /rest/dli/exports/{id}
11. POST /rest/users/requestPasswordReset - For unverified user, the server sends the password resets email with different token so that the user can be verified later on via the reset password link.
12. Several admin and end-user endpoints - If an item does not exist, 403 ERR_ACCESS_USER will be returned instead of 404 ERR_ENTITY_NOT_FOUND.
13. Folder information endpoints - a new "with" param "currentUserRole" was added to reflect the role of the current user in the requested folder.
 * GET /rest/folders/{id}?with=currentUserRole
 * GET /rest/folders/{id}/folders?with=currentUserRole
 * GET /rest/folders/{id}/top?with=currentUserRole

