The service exposes a couple of high level variables that allow you to access the service and serializer so you can set values on these objects before serialization occurs.

### Serializer
This is an instance of the JSON Serializer that is used to serialize the result. Use this value to set serializer properties like `.cPropertyNameOverrides` to force property names on object results to custom case for example.

### JSONService
This is an instance of the JSON Service (this object) which otherwise is not visible in the object methods that are called. You can use this object to set things like `IsRawResponse` to force raw `Response.Write()` results to the client for example.