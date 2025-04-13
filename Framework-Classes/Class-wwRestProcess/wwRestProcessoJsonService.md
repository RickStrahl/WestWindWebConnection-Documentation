Instance of the [wwJsonService](VFPS://Topic/_1WU1A33ET) object that's handling the request so that you can control request behaviors for the current request.

From here you can also access `THIS.oJsonService.oSerializer`.

The JsonService instance provides access to several features including:

* **IsRawResponse** - Allows you to return a non-JSON response
* **IsDebugMode** - Determines if the service is running in debug mode and how it reports errors

Also available as `JsonService` inside of service methods (as well as `Serializer` for the wwJsonSerializer instance).