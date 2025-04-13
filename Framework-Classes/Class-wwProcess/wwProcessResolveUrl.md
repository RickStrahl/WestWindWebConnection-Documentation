This method resolves a Url that starts with a generic ~/ prefix and replaces this with the base virtual path of the application. 

This method is used extensively by the Web Control Page framework to allow placing images on forms and having them rendered without having to rely on specific relative paths. Instead you can use ~/  to have the application base path replaced into the path automatically. 

The virtual path is determined through the protected GetBaseUrl() method that is called during Init of the class. 

### Example
In a Web application where the virtual web root is `timetrakker`, the following URL:

```foxpro
Process.ResolveUrl("~/admin/ProcessInvoices.ttw")
```

resolves to:
```
/timetrakker/admin/ProcessInvoices.ttw
```

Note that the URL is relative. If you need an absolute URL for embedding into emails or social media links use [wwProcess::ResolveServerUrl()](VFPS://Topic/_4LD0NH68M).