This is is the original wwApi class that requires instantiation. Most methods in the wwApi **library** don't require instantiation, only the methods here require the instance.

```foxpro
do wwApi
loApi = CREATEOBJECT("wwAPI")
? loApi.CreateGuid()
```