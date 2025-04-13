Uses an HTTP `POST` operation to post content to the Web server and to retrieve Web content from a URL. You can pass in a URL and the raw Post data to send. Make sure to set  `.cContentType` if using non-Html form data.

```foxpro
DO wwHttp
LOCAL loHttp as wwHttp OF wwhttp.prg
loHttp = CREATEOBJECT("wwHttp")

*** Post HTML Form Variables
* loHttp.cContentType="application/x-www-form-urlencoded"  && default for POST
loHttp.AddPostKey("Name","Rick")
loHttp.AddPostKey("Value","1")
lcHTML = loHttp.Post("https://yoursite.com/some/endpoint")

*** POST a JSON request to a server
lcJson = [{"name": "Rick", "company": "West Wind", "entered": "2020-10-21T08:12:33Z"}]

*** Recreate wwHttp (best practice)
loHttp = CREATEOBJECT("wwHttp")

*** Post a raw Buffer of data
loHttp.cContentType="application/json"
lcJsonResult = loHttp.Post("https://yoursite.com/some/json/endpoint", lcJson)
```

> #### @icon-info-circle JSON Service Requests using wwJsonServiceClient
> If you're sending data to JSON REST Services you can simplify the JSON serialization and HTTP calls using the [wwJsonServiceClient class](VFPS://Topic/_4JF1F19ZR) which lets simply pass and receive objects and values rather than serialized strings.


Please also see [wwHttp::HttpGet](VFPS://Topic/wwHTTP%3A%3AHTTPGet) and [Access Http Content over the Web](VFPS://Topic/Access%20HTTP%20content%20over%20the%20Web)