wwHttp supports an Authentication scheme that's similar to Internet Explorer's as wwHTTP relies on WinInet which is also used by Internet Explorer to connect remotely. As such it supports Basic Authentication and Windows Authentication. Both can be specified using the cUsername and cPassword properties, or the HttpConnect() lcUsername and lcPassword parameters.

```foxpro
oHTTP = CREATE("wwHTTP")
lcHtml = oHTTP.HttpGet("http://www.west-wind.com/administration/Secret.htm",;
                       "rickstrahl","Secret")
```

### Passing client credentials to the server with Windows Authentication automatically
If you are running an application over an Intranet - an environment where both client and server are validating against the same domain or workgroup - wwHttp will also pass the current Windows credentials to the server application for automatic login. This is the same behavior you use in Internet Explorer which will not prompt for credentials when trying to log on to a site on the current domain if your current account can validate. 

### SSL/HTTPS Urls
SSL requests are automatic if you specify an HTTPS Url in HttpGet() calls. Simply specify your URL with the https:// prefix and the request will automatically be made over HTTPS.

```foxpro
lcResult=HttpGet("https://www.west-wind.com/wwStore/")
```

If you're using HttpConnect you need to set the wwHTTP::lSecure flag or pass in the llHTTPS paramater as true.