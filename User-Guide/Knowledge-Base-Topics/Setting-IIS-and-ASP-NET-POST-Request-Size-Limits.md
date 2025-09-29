If you are planning to upload large files to your Web server, you might run into problems with IIS and ASP.NET file upload size limits, which by default are fairly small (limited to 2megs). In order to accept larger files **you have to change two sets of settings** in your `web.config`.

* IIS Web Server RequestFiltering `requestLimits` Setting
* ASP.NET Http Runtime `maxRequestLength` Setting

### IIS Setting: Request Limits
The first setting is the IIS Request Limit settings which is installed as part of the Request Filtering IIS Module. IIS Request Filtering is an optional IIS feature, but it is a required install feature when you install ASP.NET in IIS, so you can assume it will be installed if ASP.NET is enabled in IIS.

The following sets the max POST buffer size to 500 megs. The value is specified in bytes.

```xml
<configuration>
    <system.webServer>
        <security>
          <requestFiltering>
            <!-- value is in Bytes! 50mb -->
            <requestLimits maxAllowedContentLength="50000000" />
          </requestFiltering>
        </security>
    <system.webServer>
</configuration>    
```

This configures the main IIS server to allow requests of 50mb. This affects **both** direct uploads to `CGI` or `ISAPI` scripts as well as ASP.NET requests. 

### ASP.NET Setting: httpRuntime maxRequestLength
ASP.NET uses an additional setting for reasons unknown, which effectively does the same thing, but in reality can't be any bigger than the IIS setting above, since that fires and blocks first.

ASP.NET uses the `httpRuntime` element to control a number of runtime related features including the allowed inbound request length. The inbound data size is checked very early in the request cycle and makes a request fail immediately.

```xml
<configuration>
    <system.web>
        <!-- Value is in KiloBytes! 50mb -->
        <httpRuntime maxRequestLength="50000"  executionTimeout="120" />
    </system.web>
<configuration>  
```

Ideally you set these values to match in both settings. 

> ##### @icon-warning Important! Bytes vs. KiloBytes
> Keep in mind that these two values use different measurements!
> * The IIS Value is set in **bytes**
> * The ASP.NET Value is set in **kilobytes**
> 
> Both values have to be set for Web Connection to work and ideally set to the same byte count.



For more detailed info please see [this blog post](https://weblog.west-wind.com/posts/2016/Apr/06/Configuring-ASPNET-and-IIS-Request-Length-for-POST-Data#1369984)

### What about Web Connection?
Starting with Web Connection 6.0, Web Connection has no specific Post size configuration any longer for the **Web Connection .NET Handler**.

Only the IIS and ASP.NET Settings are applied.

> The deprecated ISAPI module still has a `PostBufferLimit` setting in `wc.ini`, but we recommend you leave this value at 0 and let IIS handle this setting via the Request Limits. It's better to do this at the IIS level as it's much more efficient because requests never actually enter the IIS pipeline.