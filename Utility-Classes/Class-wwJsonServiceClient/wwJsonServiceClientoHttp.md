The [wwHttp](VFPS://Topic/_0JJ1ABF2K) instance that is used for HTTP access. A **new default instance is created for every request** using the `CreatewwHttp()` method at the beginning of the request or when you first access it. 

```foxpro
loClient = CREATEOBJECT("wwJsonServiceClient")

loClient.oHttp.AddHeader("Authorization", lcBearerToken)
loClient.oHttp.nTimeout = 15

loClient.CallService(...)
```

If for some reason you need to pre-configure your HTTP object, or use your own customized version  you can explicitly create your instance and and pass it to `.CreateHttp(loMyHttpInstance)`.