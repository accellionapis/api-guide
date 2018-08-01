# Setup

## Prerequisites
You should be an experienced developer and have an understanding of the following concepts and technologies:

* OAuth 2.0
* JSON data format
* HTTP methods and status codes
* REST APIs

## Get an Accellion instance
You will need an Accellion instance to get access to the API. If you don't already have one, please click on the button below:

<a href="https://info.accellion.com/demo-request?ref=api-guide-setup" target="_blank"><img src="images/get-a-demo.png" alt="drawing" width="90px"/></a>

## Setup your first custom app
If you already have an Accellion instance:

1. Sign in to the Admin interface at https://[hostname]/admin. Replace [hostname] with your Accellion instance hostname.
2. Click on the Application tab icon using the top navigation bar.
3. Click on Client Management in the left navigation panel.
 * **NOTE**: If you see the Custom Applications section under Client Management, you have everything you need to get started. Otherwise, please email <support@accellion.com> to request API access for your instance.
4. Click on Custom Applications under Client Management.
5. Click on the + button to add your custom app. Further steps below the screenshot.
![](../images/navigation-custom-apps.png)
6. Fill up the Add Custom Application form as per your needs. Our example app will use standard OAuth 2.0 Authorization Code flow with refresh token. 
 * **NOTE**: If you want to test this app in the playground, be sure to set the Redirect URI to https://%%HOST%%/rest/callback.html
![](../images/custom-app-form.png)
7. Click on the Add Application button. Copy and save the Client Application ID and Client Secret Key in a secure location.
8. Click OK. Your custom application is now created and you are ready to start experimenting in the playground.

## Using the playground
1. On the Custom Applications page, turn the API playground ON.
2. Access the API playground at https://[hostname]/rest/index.html in your web browser. Replace [hostname] with your Accellion instance hostname.
3. Once the playground has loaded, click on "Get a token" button near the top right.
4. In the popup form, Authorization Code grant type is selected by default. Enter the ID and Secret Key of the custom app you created in the previous section. Click "Authorize and Get Token".
![](../images/playground-get-token.png)
5. Click "Grant Access" in the new End-User Authorization popup. The playground will receive a token from the Accellion server and display it in the top right. You are now ready to make your first API call from the playground.
