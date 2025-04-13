Set POST data to send on a POST or PUT request. 

This method sets the post buffer and supports encoding for HTML form based uploads as well as raw data buffers.

Several different POST mechanisms are available:

* **UrlEncoded Form Data** <small>`nHttpPostMode=1` (default)</small>
* **Multi-Part Forms**	<small>`nHttpPostMode=2`</small>
* **Raw Data** <small> `cContentType="application/json"`</small>

The two HTTP Form post modes - UrlEncoded (1) and Multi-Part (2) are supported via the `nHttpPostMode` flag which triggers specific content encoding of the key value pairs passed.

For raw Data content like JSON, XML, or binary data like images, pdfs, zip etc. you pass the data in the `tcKey` parameter, and set the `cContentType` property with the data's content type.

> For HTTP File Upload form keys using multi-part forms, please use the [.AddPostFile()](VFPS://Topic/_0JJ1AFXK3) method (new in v7).

> Note: For raw data requests you can also pass the POST data directly to the `.Post()` or `Put()` methods as parameters, instead of using `.AddPostKey()` explicitly (new in v7).