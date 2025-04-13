Instance of the wwWebAjax control used for callbacks. This object can be referenced on the client via:

**<Control.id>_Ajax**  

So if the Progress control is called progress you'd get: Prorgress_Ajax which can be used like this to make callbacks from the client to the server:

```cs
CallMethod("Progress_Ajax","StartProcessing",StartProcessingCallback);
```

from the client, where StartProcessing is a server side method and StartProcessingCallback is the client side function pointer that receives the response.