Hook method called after every REST service request. 

This method allows you to post-inpect the result from Rest Service calls. It gets passed the `@lcResult` reference parameter that contains the JSON response for the request. Because the value is passed by reference you can modify it and the modified value is sent to the `Response`.

> #### @icon-info-circle Non Data Results with `JsonService.IsRawResponse
> Be careful with `IsRawResponse` results which don't produce result string JSON output. `IsRawResponse` requests directly write output into the `Response` object and you can't intercept these values except by rewriting the response.