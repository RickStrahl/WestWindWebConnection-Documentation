An event handler that is called with the error Exception if a Json Process method fails. You can override this method to log errors or otherwise capture the error exception.
  
Unlike the page `OnError()` handler, this handler method doesn't generate any output - it only provides a mechanism for capturing the error and potentially logging it.

> Note: The `wwRestProcess::OnError()` handler **never fires** and **should not be used**, because all processing is handled internally by `wwJsonService::CallMethod()` processing and its own exception wrapping that captures all error information. `OnError()` receives that exceptino on failed requests. 


> `OnJsonError()` only captures the exception but  doesn't perform any other operations to modify the output - it should not generate any output.