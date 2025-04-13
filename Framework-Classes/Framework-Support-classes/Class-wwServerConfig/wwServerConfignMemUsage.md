Memory usage flag. This value is passed to VFP's SYS(3050) function to attempt to limit memory usage of the Web Connection application servers. 

This behavior operation can be overridden as it's set in the exposed SetServerProperties method of the server's startup code like this:

<pre>SYS(3050,2,THIS.oConfig.nMemUsage)</pre>