In order to allow remote access to your site from web browsers running on native mobile device applications or other web domains than the origin domain you have to enable a CORS response header so that the site can be accessed remotely. Specfically you have to add the Access-Control-Allow-Origin header to your Response headers.

It’s easy to set this header in Web Connection:

```foxpro
Response.AppendHeader("Access-Control-Allow-Origin","*")
```

on any request that happens. Here I’m allowing any source domain to access the site. You can also specify specific domains as a comma delimited list in the format of http://west-wind.com,http://weblog.west-wind.com for example.

Alternately you can also add this globally for an entire process class by setting the value in the OnProcessInit() method:

```foxpro
FUNCTION OnProcessInit
 
Response.GzipCompression = .T.
Request.lUtf8Encoding = .T.
 
Response.AppendHeader("Access-Control-Allow-Origin","*")
 
RETURN .T.
```

Finally you can also use IIS to set this header globally for the entire site or application assuming your using IIS 7 or later:

```xml
<configuration>
    <system.webServer>
      <httpProtocol>
        <customHeaders>
          <clear/>
          <add name="Access-Control-Allow-Origin" value="*" />
        </customHeaders>
      </httpProtocol>
    </system.webServer>
</configuration>
```

For more info please see this blog post.