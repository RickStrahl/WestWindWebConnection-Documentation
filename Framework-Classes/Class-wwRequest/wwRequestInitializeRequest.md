This method is responsible for setting up the Request object on each hit by passing in the POST data in some format and making it available to the specific Get methods. This method is fired on every Web request hit and deals with clearing out values from previous requests and then reassigning new values.

It deals with setting up the form data, configuring the querystring and setting up. Once this method complete the Request object is fully operational and can be accessed as normal.

This method is called internally only, but it's the vital piece that sets up the request properly for operation. 

If for whatever reason to choose to implement your own Request implementation make sure that this method gets called on every request that needs to be processed as this method essentially sets up the Request object to return appropriate values for its interface.