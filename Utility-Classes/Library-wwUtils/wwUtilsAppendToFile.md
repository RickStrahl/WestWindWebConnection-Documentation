Appends a raw string to the end of a file. The string is written as is without a linefeed.

This function uses low level file functions to attempt to write to the output file. The operation is very efficient.

If you want to use this for logging look at LogString() instead.