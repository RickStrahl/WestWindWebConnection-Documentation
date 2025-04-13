Uses an HTTP `GET` operation to retrieve Web content from a URL. `GET` operations pass only the URL and no content body data and expect back an HTTP response.

```foxpro
DO wwHttp
LOCAL loHttp as wwHttp

*** HTML response
loHttp = CREATEOBJECT("wwHttp")
lcHtml = loHttp.Get("https://microsoft.com")
? lcHtml

*** JSON Response
*** Prefer to recreate the object each time
loHttp = CREATEOBJECT("wwHttp")
lcJson = loHttp.Get("https://api.exchangeratesapi.io/latest")
? lcJson
```


Please also see [wwHttp::HttpGet](VFPS://Topic/wwHTTP%3A%3AHTTPGet) and [Access Http Content over the Web](VFPS://Topic/Access%20HTTP%20content%20over%20the%20Web)