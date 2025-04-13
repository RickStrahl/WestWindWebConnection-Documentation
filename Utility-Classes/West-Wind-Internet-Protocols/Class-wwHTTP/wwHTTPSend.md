This is the generic (and classical) Http operation that handles all types of HTTP requests. 

The `.Get()`,`.Post()`,`.Put()`,`.Delete()` methods are simply shortcut wrappers around this method that set the `.cHttpVerb` and meant to be more explicit for code readability.

The primary Http retrieval method of this class that retrieves HTTP content from the Web. Despite its name this method can also `POST` data to the server, in fact it can use any HTTP verb to submit data.

The method supports URL syntax, HTTPS syntax, username and password authentication, content data (via [AddPostKey()](VFPS://Topic/_0JJ1AFXK3) or the `Post()` and `Put()` method data parameters), custom Http headers (via [AddHeader()](VFPS://Topic/_20J1708ZR)) and saving large download output directly to file. You can also use `OnHttpBufferUpdate` to get download progress events.

*This method syntax also applies to the old `.HttpGet()` method. The `.Send()` method replace this deprecated method with the exact same syntax.*