Uses an HTTP `PUT` operation to post content to the server and to retrieve Web content from a URL. 

```foxpro
DO wwHttp
LOCAL loHttp as wwHttp OF wwhttp.prg
loHttp = CREATEOBJECT("wwHttp")

*** post HTML form variables
loHttp.AddPostKey("Name","Rick")
loHttp.AddPostKey("Value","1")

lcHTML = loHttp.Put("https://yoursite.com/some/endpoint")


lcJson = loApp.GetRequestJson()  && some method that gets data

*** Prefer to recreate wwHttp
loHttp = CREATEOBJECT("wwHttp")

*** post raw data buffer (JSON in this case)
loHttp.cContentType="application/json"
loHttp.AddPostKey(lcJson)

lcHTML = loHttp.Put("https://yoursite.com/some/json/endpoint")
```

`PUT` is similar to `POST` but semantically used for updates, while `POST` is semantically used for additions in REST APIs

Please also see [wwHttp::HttpGet](VFPS://Topic/wwHTTP%3A%3AHTTPGet) and [Access Http Content over the Web](VFPS://Topic/Access%20HTTP%20content%20over%20the%20Web)