This event fires after the Http connection has been established and can be useful to set specific WinInet options on the HTTP connection.

Use this event to call `SetOption()` to apply HTTP connect options. Since `SetOption()` requires a connection handle, it can only be used in the context of a request, which means you have to subclass `wwHttp` and override the `OnHttpPostConnect()` method.