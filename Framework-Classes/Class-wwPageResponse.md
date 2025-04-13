The wwPageResponse class is Web Connection's default Response class that replaces the original **wwResponse** class. This class has a more modern and leaner interface using standard mechanisms for setting the ContentType, adding headers, setting cookies, adding authentication and of course writing output. This new response class is a memory only class as it can set and update headers at any time during the Request lifetime.

At the lowest level the `Write()` method is used to generate output. You can use the `ContentType` property to set the content type for the request - things like text/hml (default) or application/json for example. You can use `AppendHeader()` to add any HTTP header to the response and `AddCookie()` to add a cookie. `DownloadFile()` and `TransmitFile()` let you efficiently serve content from disk.

Higher level methods include `ExpandScript()` and `ExpandTemplate()` to parse FoxPro code and expressions inside of HTML markup templates.

This class generates string based output that can be collected using the `RenderHttpHeader()` and `Render()` methods which return the header and body output respectively. Note that you can use wwPageResponse easily outside of Web Connection to build up HTTP responses.

Output of this class is limited to 16 megabytes due to FoxPro's string length limitations. You can use `TransmitFile()` or `DownloadFile()` to send larger content to file and then serve the file from disk over HTTP which allows for larger files and responses to be returned.

### wwPageResponse in wwProcess Classes
**wwProcess** uses the **wwPageResponse** class to generate request HTTP output. **wwPageResponse** is this  the default Response object which is equivalent to the following in the class header of your Process class:

```foxpro
cResponseClass = "wwPageRepsonse"
```

> #### @icon-info-circle Backwards Compatibility with wwPageResponse40 
> Web Connection 6.0 and later removes many legacy features that existed on the original wwResponse class which may break old applications that are upgraded. Many of these legacy functions dealt with custom headers via a clumsy function and a number of output generation routines for form controls and various high level HTML constructs like `ShowCursor()` and `DbfPopup()`. All of this functionality is now available in more modular fashion in other places in the framework. Header manipuluation has been moved to `Response.AppendHeader()`, `Authentication()` and `AddCookie()`, while most of the HTML generation routines can now be found in much more flexible helpers in **[wwHtmlHelpers](VFPS://Topic/_3361EQOP8)**.
>
> While we recommend you replace any legacy functionality with new features for consistency and much cleaner code, you can fall back to the **wwPageResponse40** class which includes all the legacy features. To use this class simply change the `wwProcess::cResponseClass` in your custom Process class: 
>
> ```foxpro
cResponseClass = "wwPageRepsonse40"
```
>
> This class should allow existing applications to use the new Page class functionality without any major changes.