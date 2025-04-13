If set to .T. throws errors as exceptions when lStopOnError = .F.

Note that this will stop processing the template in all cases and return the original error information for an exception handler that is hooked up.

If .F. no error is thrown. Instead a generic error response is created.


In both cases the `oException` and `lError` and `cErrorMsg` properties are set on the wwScripting instance.