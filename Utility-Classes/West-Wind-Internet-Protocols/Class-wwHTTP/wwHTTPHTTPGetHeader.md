Retrieves just the HTTP header from a URL. Useful for retrieving information about a request prior to actually downloading the full URL. You can spy the content length from the header for example.

In download scenarios, you might want to use the cHTTPHeaders property and the OnHTTPBufferUpdate() event to trap for the HTTP header as part of a running request. The first buffer grab returns the HTTP header.

The cHTTPHeaders property is also set by this method so passing in the 2nd and 3rd parameters is optional.