# APIs Overview  

kiteworks APIs provide broad coverage of the platform. The APIs can be categorized into **Content**, **Collaboration**, **Preferences**, **Contacts**, **Security**, **Clients**, and **kiteworks Maintenance APIs**.

## Content APIs
Content-related APIs provide access to user content in your application. You will be able to access and manage files and folders as a part of the business flows of your app and work with files from various enterprise content sources like Microsoft SharePoint or EMC Documentum.

### users
**users** APIs enable your application to obtain basic information about the user, user's root folders, and provide a starting point to further navigate through the files and folders the user has access to. By using the **/users/me** endpoint, you can obtain the ID of the user, the IDs of the root folder for this user, the email address or the name of the user, and status of the user (active, deleted).  

Complete details of the **users** API is available at _your installation URL>/rest/index.html#!/users_.  

### folders  
The next step is to work with the files and **folders** accessible to the authenticated user.  

Complete details of the **folder** API is available at _your installation URL>/rest/index.html#!/folders_.   

### files  
Together with folders, **files** are another fundamental entity that your application will have at its disposal. 

Complete details of the **files** API is available at _your installation URL>/rest/index.html#!/files_.

### sources  
One of the advantages of the kiteworks Content Platform is its ability to securely connect to existing enterprise content sources through a single user interface. Using the **sources** APIs, your application can access and manage EC content sources in a similar fashion.

Complete details of the **sources** API is available at _your installation URL>/rest/index.html#!/sources_.

## Collaboration APIs
The collaboration-related APIs are intended to provide your application with the powerful collaboration tools that users have in kiteworks. In addition to being able to invite users to shared folders, these APIs allow users to collaborate on files and folders, construct and receive mail, add comments, and assign tasks.

### mail
The **mail** APIs allow you to access emails sent and received on behalf of the user authenticated through your application.

Complete details of the **mail** API is available at _your installation URL>/rest/index.html#!/mail_.


### comments
In addition to comments-related endpoints in **/files**, the **/comments/** endpoints allow you direct access to existing comments for update and delete actions.

Complete details of the **comments** API is available at _your installation URL>/rest/index.html#!/comments_.


### tasks
Similar to the **/comments** endpoint, the **/tasks/** endpoints allow you direct access to existing tasks for update and delete actions.

Complete details of the **tasks** API is available at _your installation URL>/rest/index.html#!/tasks_.

 
## Preferences APIs
kiteworks Content Management platform provides a set of APIs for preferences-related entities: **folder notifications**, **favorite folders**, **languages**, and **time zones**.


### notifications
The **notifications** entity endpoints allow the management of notification settings for important folders in the system for the given user.

Complete details of the **notifications** API is available at _your installation URL>/rest/index.html#!/notifications_.

### favorites
The **favorites** entity endpoints enable your application to manage the favorite folders for the authenticated user. 

Complete details of the **favorites** API is available at _your installation URL>/rest/index.html#!/favorites_.

### languages
The **languages** APIs provide your application with the ability to retrieve the languages supported by the kiteworks system.

Complete details of the **languages** API is available at _your installation URL>/rest/index.html#!/languages_.

### timezones
The **timezones** APIs enable your application with the ability to list all the time zones supported in the system, and get the details about the time zones, like the name and time offset.

Complete details of the **timezones** API is available at _your installation URL>/rest/index.html#!/timezones.

## Contacts APIs
**contacts**
The **contacts** APIs provide your application with the ability to manage the user contacts.

Complete details of the **contacts** API is available at _your installation URL>/rest/index.html#!/contacts_.

### groups
kiteworks provides end users with the ability to define personal contact **groups**. Personal groups can be used for allowing access to a folder, or for sending mail.

Complete details of the **groups** API is available at _your installation URL>/rest/index.html#!/groups_.

### LDAP Groups
If your installation is integrated with LDAP, Administrators can enable **LDAP** groups to be available to end users when sharing folders. Your application will be able to add, update, or remove **LDAP** groups that are available to end users by utilizing the **/ldapGroups/** endpoints.

Complete details of the **ldapGroups** API is available at _your installation URL>/rest/index.html#!/ldapGroups_.

## Security APIs
### profiles
The **/profiles/** endpoints allow your application to manage the privileges assigned to kiteworks users. Your application can identify the list of User Profiles in the system and inspect the features and settings associated with each User Profile.

Complete details of the **profiles** API is available at _your installation URL>/rest/index.html#!/profiles_.

### roles
The **roles** APIs allow you to get the details of folder roles in the system.

Complete details of the **roles** API is available at _your installation URL>/rest/index.html#!/roles_.

### adminRoles
The **adminRoles** APIs allow your application to manage the assignment of Administrator roles to users.

**NOTE**:	In order to use the **adminRoles** APIs your application will need to authenticate with an Administrator user. Additionally, only System Administrator can promote a user to System Administrator, Application Administrator. A user cannot self-promote to a System Administrator.

Complete details of the **adminRoles** API is available at _your installation URL>/rest/index.html#!/adminRoles_.

### devices
Users may access kiteworks from various devices: mobile phones, tablets, etc. Device endpoints can be used to track access of individual **devices** to user accounts, and perform remote wipe on any device.

Complete details of the **devices** API is available at _your installation URL>/rest/index.html#!/devices_.

### admin
The **admin** APIs allow your application to perform administrative actions on many entities that exist in the system.
The endpoints are very similar to the endpoints of the actual entities, the difference is that your application will need to authenticate with an Administrator user in order to perform the calls.

*	**Client Applications**: using **/admin/clients/** endpoints, you can create and configure the configuration settings of the client applications registered with kiteworks, and list API Scopes available.
*	**Devices**: using **/admin/devices/** endpoints you can find the list of user devices that are allowed to connect to kiteworks, log their access to the platform, and update, or remove them as necessary
*	**LDAP Groups**: List, create or delete LDAP groups in the system
*	**License**: Upload a new kiteworks license
*	**Locations**: List, create, or delete kiteworks Locations: requires System Administrator, and is applicable for on-premises Enterprise and Enterprise Connect Packages only
*	**Profiles**: List user Profiles. For details on profiles please refer to kiteworks Administration Guide
*	**Sources**: List, add, update, remove Enterprise Connect sources
*	**Users**: List, add, update, delete users, user settings, admin roles, profile image.

Complete details of the **admin** API is available at _your installation URL>/rest/index.html#!/admin_.

## Client Management APIs
### clients
Using the **clients** APIs, you can register a new application, and manage the configurations for the applications allowed to connect to kiteworks.

Complete details of the **clients** API is available at _your installation URL>/rest/index.html#!/clients_.

### scopes
One of the main security and safety mechanisms for preventing unauthorized or accidental application use of the platform resources is the Administrator ability to set the API **scopes** for each application. kiteworks Content Platform provides the **/scopes** endpoint that allows applications to determine the APIs supported by the platform, so the application can properly construct the API calls to the platform.

Complete details of the **scopes** API is available at _your installation URL>/rest/index.html#!/scopes_.

## kiteworks Maintenance APIs
### licenses
If your application is in charge of updating the license of your kiteworks installation it can use the **/licenses** endpoint to upload a new license.

Complete details of the **licenses** API is available at _your installation URL>/rest/index.html#!/licenses_.

