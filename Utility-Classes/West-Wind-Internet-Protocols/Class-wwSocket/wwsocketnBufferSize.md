The default Receive buffer size in bytes. This size is used to determine how much data the socket waits for at a time before returning control to you. 

Note that this doesn't guarantee that the buffer is always filled even if more data is forthcoming as the server determines how data is chunked to the client. This setting is overridden in calls to Receive() or WaitFor() when the lnSize parameter is passed.

Default: 512