> ##### @icon-warning Do not Implement!
> Do not override this Event Handler - it is not used or fired by typical processing.   
>**Leave it as-is and do not implement**.

If you need to intercept errors from Json request methods use the [wwRestProcess::OnJsonError](dm-topic://_o7b75vrgfd) handler instead.

### Background

The `wwRestProcess::OnError` handler is not used in this process class, because it uses the `wwJsonService` class to process the Json method externally. 

The service handles the error and then calls `OnJsonError(loException)` to allow you to capture the exception for logging or other task with storing the exception. Unlike `OnError` the `OnJsonError()` method does not and should not modify output - it's an informational event only.

### So why is the OnError handler here?
It's a subclass of `wwProcess` and it's a base FoxPro event so the method has to be present. Furthermore it has a base implementation that returns a JSON error response for those very remote edge cases that occur outside of JsonService processing that might trigger an error.