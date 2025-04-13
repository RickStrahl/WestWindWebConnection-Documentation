Returns a custom HTTP Header sent from the client.

Extra Headers are custom headers posted by client applications to send special processing instructions to the server. For example, SOAP includes a SOAPMethodName extra header to specify the URI and name of the method to call.

### Examples

```http
GET http://localhost/wconnect/HttpHeader.wwd HTTP/1.1
Accept-Encoding: gzip,deflate
SOAPMethodName: GetCustomers
custom-header: Hello world
```

Extra Headers can be retrieved in a couple of ways:

##### Header Name
```foxpro
lcHeader = Request.GetExtraHeader("SOAPMethodName")
lcHeader = Request.GetExtraHeader("custom-header")
```

##### HTTP Server Variable Name
```foxpro
lcHeader = Request.GetExtraHeader("HTTP_SOAPMethodName")
lcHeader = Request.GetExtraHeader("HTTP_CUSTOM_HEADER")
```