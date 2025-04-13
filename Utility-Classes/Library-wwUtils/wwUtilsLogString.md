Appends a log string to the end of a file. The line is written with a timestamp and a CRLF at the end of the line.

This function uses low level file functions to attempt to write to the output file. The operation is very efficient.

The log format looks like this:

```foxpro
12/03/2015 11:17:57 AM - i: drive couldn't be mapped
12/03/2015 11:19:57 AM - Failed to initialize SQL Connection
```

Multiline strings are allowed but will make the output of the file very ugly.