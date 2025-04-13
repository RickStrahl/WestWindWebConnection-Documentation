Clears all current output from the HTTP output stream. The object remains valid, but output has to start over. 

This method is used internally to clear the output buffers when errors occur - at that point output is reset and a full new HTTP response is written typically by [wwProcess::ErrorMsg](vfps://Topic/wwProcess%3A%3AErrorMsg).