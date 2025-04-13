Flags that are used on the HTTP connection in WinInet. This method is needed to pass options that require setting values. For option switches you can use the `.nHttpServiceFlags` or `.nServiceFlags` settings instead.

Typically `SetOption()` has to be called from within an active request because it needs a connection handle, which means you have to subclass wwHttp and override the [OnHttpPostConnect()](VFPS://Topic/_0MS0MK4QP) method.

Option values are WinInet constants which can be found here:

[WinInet Option Flags (Microsoft Docs)](https://docs.microsoft.com/en-us/windows/win32/wininet/option-flags?redirectedfrom=MSDN)