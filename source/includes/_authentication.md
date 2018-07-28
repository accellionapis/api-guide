# Authentication
This section describes how to authenticate and get tokens to start making API calls. Perform the following steps to authenticate and start your custom application development.

Step 1. Sign in to kiteworks.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Once you have your instance of kiteworks up and running, sign in to the kiteworks admin interface with your user credentials. The admin interface can be accessed from the hostname of your kiteworks server/admin.

Step 2. Create your custom application to obtain the identifying information: the Client ID and Secret Key.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; On the kiteworks Administrator’s Dashboard, go to **Application** > **Client Management** > **Custom Applications** and add your custom application.

![](../images/customapp.png)

![](../images/addcustomapp.png)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Specify a Custom Application Name. This name will be used by the Administrator for the Client Application Management and Reporting and in the Consent Form to authorize new users.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Select the authentication flow that your application will use to obtain an access token. Authorization Code- Standard OAuth 2.0 authorization-code consists of authorization, consent and the code redemption process.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Under Flows select **Authorization Code**.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Specify the **Redirect URI** using this format https://%%HOST%%/rest/callback.html

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Choose your **Access Token Lifetime**.

Step 3. Add your Custom Application. You will be given the Client Application ID and Client Secret Key for your application.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **IMPORTANT:** You must copy this information and keep it in a secure location. The Client Secret Key is required for authenticating your app. If you lose this information, you will have to start over and re-register your app.

Step 4. Configure your Custom Application. The kiteworks APIs are used by the custom applications to access user resources on a kiteworks server.

The kiteworks APIs are used by the custom applications to access user resources on a kiteworks server.

**API Usage** – The APIs follow the REST architectural style and use the scheme of addressing a resource and invoking a method on that resource.

**The API URI** – All APIs can be called using the following URI scheme: https://<hostname>/rest/<resources>
  
**API Output** – The API result is returned in JSON format.
