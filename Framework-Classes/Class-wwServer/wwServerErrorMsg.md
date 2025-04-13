Creates a self contained error page. Used internally to create error pages that occur during startup processing and when an invalid URL is passed to Web Connection.

This method immediately sends results back to Http client and should only be called when the request is otherwise complete.

> Do not call this from Process Class code - this method should be internal only.