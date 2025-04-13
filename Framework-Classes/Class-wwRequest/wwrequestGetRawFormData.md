Returns all of the form data in raw form.

Use this method to retrieve the POSTed data in its entirety which is a common scenario if you are dealing XML or binary data posted from a thick client. 

You can also use this feature to 'save' form data from a request for logging or potenially reassigning or 'playing' back form data in another request.

If the data is XML you can set the [wwRequest::lUseXMLFormVars](vfps://Topic/wwRequest%3A%3AlUseXMLFormvars) property to treat the XML data as your form variables - [wwRequest::Form()](vfps://Topic/wwRequest%3A%3AForm) will retrieve values from the XML elements transparently.

**Assigning Post Buffer Data**  
Please note that the raw form data is also accessible via Request.cFormVars, but this string contains a leading & that is stripped by this method. If you ever need to assign a complete form variable buffer you can assign it like this:

```foxpro
Request.cFormvars = "&" + lcMyPostBuffer
```