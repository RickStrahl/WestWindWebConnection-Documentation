wwHttp uses WinInet's Cookie handling functionality which comes in two flavors:

* **Automatic Cookie Handling** *(default)*  
Cookies are automatically tracked from server requests, but you can't manually set cookies explicitly. A cookie session is the lifetime of the process.

* **Manual Cookie Handling**  
Cookies have to be manually sent on every request and there's no automatic cookie tracking between requests.

## Automatic Cookie Handling
Automatic Cookie handling picks up cookies from the system browser and automatically stores and caches any cookies that you create as part of your current process. In short, it behaves similar to the way a browser caches cookies.

> Using automatic cookies **you cannot manually add new cookies** to a request. Cookies are only captured from server HTTP requests.

Automatic Cookie handling is the the default behavior for wwHttp.

For example:

```foxpro
loHttp = CREATEOBJECT("wwhttp")
loHttp.AppendHeader("Cookie", "test=Rick")
lcHtml = loHttp.Get("https://west-wind.com/wconnect/testpage.wwd")
```
This request **will not** add the `Cookie:` header to the current request sent to the server, and instead use whatever previous cookies were set either by the system, or previous wwHttp requests in this session.

> Note that since Microsoft removed Internet Explorer, there is effectively no 'system' browser cache for cookies any longer. FireFox, Microsoft Edge and other Chromium browsers do not contribute into the 'system' cache so don't expect previously set cookies set in these browsers to show up. This refers only to IE based browsers or requests made via the Windows HTTP System (wwHttp, WinInet, WinHttp, XmlHttp etc.).

## Manual Cookie Handling
You can override the default cookie behavior by using an HTTP request flag named `INTERNET_FLAG_NO_COOKIES` to explicitly use manual Cookie mode.

In manual cookie mode, there's no system or session tracking of cookies, and only cookies that you explicitly set are applied to the current request. This means cookies have to be sent on each and every request explicitly.

> Using Manual Cookies you explicitly have to provide **all** cookies to the server on **every request**. There's no cookie caching of any sort.

Using Manual Cookies you can now explicitly set cookies.

```foxpro
#include wconnect.h
loHttp = CREATEOBJECT("wwhttp")
loHttp.AppendHeader("Cookie", "test=Rick")

*** This flag sets manual cookie mode
loHttp.nHTTPSERVICEFLAGS = INTERNET_FLAG_NO_COOKIES

lcHtml = loHttp.Get("https://west-wind.com/wconnect/testpage.wwd")
```

Now the `Cookie:` header is set with the custom cookie.