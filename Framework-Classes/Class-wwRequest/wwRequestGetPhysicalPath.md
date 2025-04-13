Returns the physical path to a script mapped page or an executable DLL file. The physical path is a great tool for capturing the system specific path of script mapped pages, so you can capture the location of the page for further parsing.

For example, Web Connection uses the Physical Path to capture scripts and uses the physical path to read in the original page, then parses it using the wwVFPScript script parser. Regardless of where the page was called from the physical path always returns the correct location for the file.

For example:

<pre>http://localhost/wconnect/scriptdemo.wcs</pre>

returns:

<pre>c:\inetpub\wwwroot\wconnect\scriptdemo.wcs</pre>