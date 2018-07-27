# Authentication
This section describes how to authenticate and get tokens to start making API calls. Perform the following steps to authenticate and start your custom application development.

Step 1. Sign in to kiteworks.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Once you have your instance of kiteworks up and running, sign in to the kiteworks admin interface with your user credentials. The admin interface can be accessed from the hostname of your kiteworks server /admin.

Step 2. Create your custom application to obtain the identifying information: the Client ID and Secret Key.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; On the kiteworks Administrator’s Dashboard, go to **Application** > **Client Management** > **Custom Applications** and add your custom application.




![](https://github.com/accellionapis/api-guide/blob/acctest/source/images/custom_application.png) 








Step 3. Add your Custom Application. You will be given the Client Application ID and Client Secret Key for your application.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **IMPORTANT:** You must copy this information and keep it in a secure location. The Client Secret Key is required for authenticating your app. If you lose this information, you will have to start over and re-register your app.

Step 4. Configure your Custom Application. The kiteworks APIs are used by the custom applications to access user resources on a kiteworks server.

The kiteworks APIs are used by the custom applications to access user resources on a kiteworks server.

**API Usage** – The APIs follow the REST architectural style and use the scheme of addressing a resource and invoking a method on that resource.

**The API URI** – All APIs can be called using the following URI scheme: https://<hostname>/rest/<resources>
  
**API Output** – The API result is returned in JSON format.
