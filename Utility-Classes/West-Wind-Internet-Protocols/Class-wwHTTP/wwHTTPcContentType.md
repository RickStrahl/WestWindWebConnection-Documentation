Sets the Content Type for **POST** data sent to the server.  Use the content type if you're posting raw POST buffers for data like JSON (`application/json`) or XML (`text/xml`) or raw binary data. 

> If you're posting HTML style form data using Url Encode or Multi-Part forms, look at the [nHttpPostMode](VFPS://Topic/_0JJ1AV2KI) property which allows you to specify key value pairs to build a POST buffer.

If neither `nHttpPostMode` or `cContentType` are explicitly set, the default active POST mode is `nHttpPostMode=2` which is url encoded form data with a content type of `application/x-www-form-urlencoded`.