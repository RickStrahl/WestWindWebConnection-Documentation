In order to release a WWWC COM object from a visual form the request has to run over HTTP to release the server. When you click on the server's Exit button for example the code actually launches an HTTP session (via wwIPStuff) and then releases the server via this link.

Default: */wconnect/wc.dll/maintain?release*