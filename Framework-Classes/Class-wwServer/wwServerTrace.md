Trace logging method that allows you to write out trace messages to a trace log file. The default file is **wcTraceLog.txt** and is written to the server's startup folder. 

This method is useful if you need to debug code that you can't debug directly such as COM Server or application startup code. Because the Server object is always available this method can be accessed from anywhere within the application with:

```foxpro
Server.Trace("OnInit Started")
...
Server.Trace("OnInit Completed")
```

The log file writes each entry with a date plus the string. While you can write out any string value, it's best to write single line entries for easier reading and parsing of the log data. However, long strings or detail values are supported.

Here's what the trace output looks like:

```foxpro
2015-03-13 14:04:35 - OnInit Started
2015-03-13 14:04:35 - OnInit Complete
2015-03-13 14:04:35 - OnLoad Started
2015-03-13 14:04:35 - OnLoad Complete
```