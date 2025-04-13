If you are planning to upload large files to your Web server, you might run into problems with IIS and ASP.NET file upload size limits, which by default are fairly small (limited to 2megs). In order to accept larger files you have to change two sets of setting in your `web.config`.

### IIS Setting: Request Limits
The first setting is the IIS Request Limit settings which is installed as part of the Request Filtering IIS Module. IIS Request Filtering is an optional IIS feature, but it is a required install feature when you install ASP.NET in IIS so you can assume it will be installed if ASP.NET is enabled in IIS.

The following sets the max POST buffer size to 500 megs. The value is specified in bytes.

```xml
<configuration>
    <system.webServer>
        <security>
          <requestFiltering>
            <requestLimits maxAllowedContentLength="500000000" />
          </requestFiltering>
        </security>
    <system.webServer>
</configuration>    
```

### ASP.NET Setting: httpRuntime maxRequestLength
ASP.NET uses the `httpRuntime` element to control a number of runtime related features including the allowed inbound request length. The inbound data size is checked very early in the request cycle and makes a request fail immediately.

```xml
<configuration>
    <system.web>
        <httpRuntime maxRequestLength="500000000"  executionTimeout="120" />
    </system.web>
<configuration>  
```

For more detailed info please see [this blog post](https://weblog.west-wind.com/posts/2016/Apr/06/Configuring-ASPNET-and-IIS-Request-Length-for-POST-Data#1369984)

### What about Web Connection?
Starting with Web Connection 6.0 the **.NET Handler no longer checks the `PostBufferSize`** that was used in previous versions. This value is now deferred in favor of the IIS configuration values above in order to remove 'yet another setting' from the equation.

The ISAPI module still has a `PostBufferLimit` setting in `wc.ini`, but we recommend you leave this value at 0 and let IIS handle this setting via the Request Limits. It's better to do this at the IIS level as it's much more efficient because requests never actually enter the IIS pipeline.