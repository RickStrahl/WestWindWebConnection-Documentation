Sets the content type of the request for example: text/plain or text/xml. If this value is not set the default content type for a request is always text/html. 

Note that you should always set a content type header explicitly since it's more efficient

This property is here only for compatibility with ASP and it simply causes the [ContentTypeHeader](vfps://Topic/wwResponse%3A%3AContenttypeHeader) method to be called directly via Assign method.