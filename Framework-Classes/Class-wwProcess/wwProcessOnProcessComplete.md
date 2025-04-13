This event is fired after the request processing has completed, but before the Process object is disposed and the response is sent back to the client. 

When this event fires all the intrinsic objects (Response, Request, Server etc.) are still available and you can potentially still modify the output sent back to the client by pushing data into the Response object.