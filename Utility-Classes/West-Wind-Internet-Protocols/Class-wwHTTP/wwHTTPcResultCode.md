Returns the HTTP result code from a request. These are HTTP spec result codes that are typically returned in the HTTP response header from the Web Server. 

A valid response from a server is "200" or "304" (cached). Most other result codes are errors or redirect notices. You can also check the cResultCodeMessage property for a string message of the simple HTTP error message.

Typically checking result codes is not important, but in some situations where you're unsure of the URLs you are hitting you might need to check the result code for authentication or moved content. Here's an example:

```foxpro
oHTTP = CREATEOBJECT("wwHTTP")

lcFileName = "d:\temp\wwipstuff.zip"

* ** Note: Invalid filename results in 404 error
lcFileContent = oHTTP.HTTPGet("http://www.west-wind.com/files/wwipstusff.zip")

* ** Check for hard HTTP protocol/connection errors first
IF oHTTP.nError # 0
   ? oHTTP.nError
   ? oHTTP.cErrorMSg
   RETURN
ENDIF

* ** Then check the result code
IF oHTTP.cResultCode # "200" AND oHTTP.cResultCode # "304"
   ? oHTTP.cResultCode
   ? oHttp.cResultCodeMessage  && Echo message from server
   RETURN
ENDIF

* ** Write content to disk
FILETOSTR(lcFileContent,lcFileName)

* ** Display downloaded file
IF FILE(lcFileName)
   GoUrl(lcFileName)
ENDIF
```

Only the final header is returned so if you have a redirect the redirect result value is returned not the 302 moved header but the 200 of the final document.

Note that this property might be important in some situations as wwHTTP will treat a server 500 error as a valid response. For example, some SOAP servers return error result structures even though they are returning a 500 error response. You can still retrieve the content as usual but know that the response is an error result. Likewise 404 (not found errors) sometimes return additional information in the content which you can also retrieve with the standard content mechanism.

Most HTTP servers return a descriptive HTML response to a 500 (or 404) error and this response is faithfully returned by wwHTTP as the result content. 

So if you need to discard output from a failed request you might want to add checking for the cResultCode property.