Allows you to override the HTTP Verb for the request. 

Use this option if you need to set an HTTP verb other than GET or POST. The default is blank which means GET or POST is auto-selected based on whether data is in the cPostbuffer property when the request is sent. If cHttpVerb is set the explicit verb is used.

The cHttpVerb property is reset after each request and has to be reset.