Uses an HTTP `DELETE` operation to retrieve Web content from a URL.


```foxpro
DO wwHttp
LOCAL loHttp as wwHttp OF wwhttp.prg
loHttp = CREATEOBJECT("wwHttp")
lcJsonResponse = loHttp.Delete("https://somesite.com/customers/11")
? lcJsonResponse
```

Please also see [wwHttp::HttpGet](VFPS://Topic/wwHTTP%3A%3AHTTPGet) and [Access Http Content over the Web](VFPS://Topic/Access%20HTTP%20content%20over%20the%20Web).