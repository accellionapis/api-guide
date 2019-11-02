# Using API Requests

Before moving onto the next exercise, let's dig into the response of the request we just made. There are a couple of important things to highlight. If you look at the response header, you will see a field called X-Accellion-Location, which indicates what object is being acted upon. 
NOTE:  In the case of an error, this field might not indicate the object being acted upon. Please consult our error-handling documentation for those cases.)

For instance, this is the response header for the request we just made.


![](../images/respheader.png)

As you can see, the object   being acted upon is the current user. In this case, the information about this user is just being returned. However, when adding, removing, updating, or otherwise editing objects, it is often critical to make note of which object   is being acted upon.

The response of the request is the JSON object that we previously saw in the UI of the developer documentation page.

Here, it is important to note that every object has an id element, which is the unique identifier for the object with respect to all other objects of that entity type. Most of the endpoints that get and/or modify a specific data will require the id of the object that corresponds to the data. Such an endpoint will be outlined in the next section, as well as how to obtain the id value for the request.
