Returns the unique Request ID for the currently executing request.

This ID is generated in the ISAPI extension and passed to your code via the REQUESTID server variable. This ID is logged in the Web Connection Log. It also gets logged in the ISAPI extension when an error occurs there, or when ISAPI Logging is enabled.