# Terminology
**Access Token** - The token is used by the API client for authorization and can be used to make API calls.  There is a  **lifetime** given to each Access Token, and this is configured by the admin in **Application** > **Client Management** > **Custom Applications**, per client application.  After the lifetime is over, the token expires and is unusable. A **refresh token** can be used to obtain a new  **access token** after it expires. 

**API call** - An API call is a general term for a HTTP request that is sent from the client to the server, to perform some kind of action.  Common **HTTP methods** are **GET** and **POST**.  GET requests data from a specified resource.  POST submits data to be processed by a specified resource.  See https://www.w3schools.com/tags/ref_httpmethods.asp for more information on HTTP methods.

**Client/Server** - The API client is the program that is created to interact with the kiteworks server and make HTTP requests for the desired actions.  For example, the AAA is a **client** that invokes the kiteworks APIs, to perform actions like uploading, sending or downloading files and emails on kiteworks.  Customers can create their own client applications in the admin interface under **Application** > **Client Management** > **Custom Applications**.

**Endpoint** - An endpoint is just a reference to a URI that accepts HTTP requests (for example, in https://meowworks.accellion.net/rest/users/me is an **endpoint**).  Endpoints and resources are very similar, but the **endpoint** is the entire **URI**.

**Flow** - The flow refers to the **authentication flow** used by the client application.  This is typically **Authorization Code** or **Signature Based Authorization Code** or **SAML 2.0** (SAML 2.0 is less commonly used).  In the API Playground, there are more flows available when you click **Get Token** however these are **not available** to be used and have not been implemented. 

*	Authorization code: standard OAuth 2.0, consent and code redemption process.
*	Signature Based Authorization Code: use this flow with the application can verify the identity of the user. This should only be used for trusted applications.  

**OAuth Playground** - The API playground is a sample client that also serves as "documentation" for our API endpoints.  It allows an application admin to test out API calls, and see how they work - what the **resource** is, **HTTP method**, as well as the required **parameters**. 

**Parameters** - Each **API call** has a set of required and/or optional **parameters** which tell the server information about the resource that is being accessed.  For instance, when using /rest/users/{id} a required parameter is user ID (in order to look up a specific user) which can be obtained with another **GET** call (GET /rest/users) that returns a list of users and their user IDs.

**Refresh Token** - Once an **Access Token** expires this can be used to obtain a new one - there is also a lifetime configured for this in Application > Client Management > Custom Applications, per client application.

**Resource** - A **resource** is an object or set of objects exposed at the API endpoint - for example "/rest/users/me" is a resource.

**REST** - (Representational State Transfer) 
*	Resources expose easily understood directory structure URIs.
*	Representations transfer JSON or XML to represent data objects and attributes.
*	Messages use HTTP methods explicitly (for example, GET, POST, PUT, and DELETE).
*	Stateless interactions store no client context on the server between requests. State dependencies limit and restrict scalability. The client holds session state.
## Common Issues
*	Sometimes customers confuse Signature Based Access with Signature Based Authorization Code when using the API playground.   Signature Based Access only works for a single API call, and doesn't provide a token with a lifetime.  
