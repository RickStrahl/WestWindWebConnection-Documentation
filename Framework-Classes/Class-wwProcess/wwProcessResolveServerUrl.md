Resolves a virtual path that includes ~ at the beginning to a fully qualified HTTP url. 

Use this method to get a fully qualified Web URL when you need to create links for externally sent links like emails or social media embedded links where relative links will not work properly.

### Example
In a Web application where the virtual web root is `timetrakker`, the following URL:

```foxpro
Process.ResolveServerUrl("~/admin/ProcessInvoices.ttw","https")
```

resolves to:
```
https://west-wind.com/timetrakker/admin/ProcessInvoices.ttw
```