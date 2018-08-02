# Playground
## Get a token
1. Once the playground has loaded (resource list has been fetched), click the "Get a token" button near the top right.
2. In the popup form, Authorization Code grant type is selected by default. Enter the Client App ID and Client App Secret Key of the custom app you created in the previous section.
3. Click "Authorize and Get Token".
![](../images/playground-get-token.png)
4. Click "Grant Access" in the new End-User Authorization popup. The playground will receive a token from the Accellion server and display it in the top right. You are now ready to make your first API call from the playground.

## Execute API Calls
Now that the playground has the access token, it is time to perform a basic API call.

On the playground webpage, you will see several categories like activities, admin, etc. Clicking on a category name expands it, showing all the endpoints in it. The categories are arranged alphabetically.

Scroll down and click the **users** category.

![](../images/users.png)

The endpoints are color coded based on the HTTP method. To the right of each endpoint, there is a brief description of its purpose.

Click on the endpoint **GET /rest/users/me** to see more details about it.

![](../images/getrestusersme.png)

Click the **Try it out!** button. In the **Response Body** section, you will see a JSON object of the **users** class, and it will contain the current user's information.

![](../images/tryitout.png)



