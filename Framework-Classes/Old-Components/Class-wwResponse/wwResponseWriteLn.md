Writes output to the HTTP output stream with trailing carriage returns. This is identical to:

<pre>Response.Write(tcText + CRLF)</pre>

but is easier to type and read. Use this for convenience, but keep in mind there's a little overhead in WriteLn since it calls back onto the Write() method to actually dump to the HTTP stream.