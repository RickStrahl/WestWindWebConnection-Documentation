Event method that is called when an error occurs during processing. The exception is passed.

The default implementation assigns properties of the wwJsonService class and so any subclass should call DoDefault(loException) first. After that call the ErrorOccurred, LastException, and ErrorMessage properties can be customized as needed or any custom error hooking code can be applied.