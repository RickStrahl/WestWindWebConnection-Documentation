Checks to see if a form variable exists in the request POST buffer.

> Note that some input controls like checkboxes and radio buttons will not return a value on unchecked/unset behavior.

This method returns .T. if the key exists even if the value is blank.

It will only return .F. if the key doesn't exist at all in the POST buffer. If you need to check for blank you can read the key with
[wwRequest::Form()](vfps://Topic/wwRequest%3A%3AForm).