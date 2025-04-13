The wwJsonSerializer instance that is used to serialize the outgoing and incoming data. This property is set on initialization and available for customization so you can set things like `oSerializer.PropertyNameOverrides` for example.

You can override `oSerializer` any time before `CallService()` is called by either directly setting the `oSerializer` property or by calling `CreateSerializer()`.


```foxpro
loClient = CREATEOBJECT("wwJsonServiceClient")

loClient.oSerializer.PropertyNameOVerrides = "firstName,lastName,lastAccess"
loClient.oHttp.AddHeader("Authorization", lcBearerToken)

loClient.CallService(...)
```