The wconnect.h file contains a large variety of settings that control how Web Connection works. wconnect.h is required by almost every code module in the framework. It's highly recommended that you include wconnect.h with your code as well. 

**Tip:**<blockquote>
DO NOT CHANGE SETTINGS IN THIS FILE! This file will be updated every time Web Connection is updated so any changes you make here will be overridden. Instead you can make changes in wconnect_override.h as described in the [Customizing wconnect.h settings](vfps://Topic/Customizing%20wconnect.h%20settings%20the%20easy%20way) topic.
</blockquote>
<H3>wconnect.h values</H3>
The following settings are important or require occasional overriding in your wconnect.h file.


<pre>#DEFINE DEBUGMODE .T.</pre>
The DEBUGMODE switch is used to switch between development mode and deployment mode. When set to .T. all errors inside the framework or your Web code causes a FoxPro error and the code to stop running on the line of code the error occurred. When the flag is .F. all errors are handled through the framework's Error method handlers. Typically an error results in an error result page.

<pre>#DEFINE SERVER_IN_DESKTOP	.F.</pre>
Determines whether the Web Connection server runs inside of the VFP frame or on the Desktop when running in file based operation. For COM servers this switch is ignored - COM server don't need a server form and when they do use it it's always a desktop form.

<pre>#DEFINE WWXML_USE_VFP_XMLTOCURSOR   .F.</pre>
Determines whether wwXML uses VFP 7 and later's XMLTOCURSOR and CURSORTOXML to create and parse XML. If this flag is .T. in general wwXML will perform better.

<pre>#DEFINE WWWC_FILTER_UNSAFECOMMANDS	.F.</pre>
This flag determines whether wwRequest::Form() and QueryString() filter commands for VFP methods that might be dangerous. Blocks things like EVAL, EXECSCRIPT etc. to prevent execution from hijacking of user interput.

<pre>#DEFINE DEFAULT_HTTP_VERSION	"1.0"
#DEFINE DEFAULT_CONTENTTYPE_HEADER ;
    "HTTP/1.0 200 OK" + CR + ;
	"Content-type: text/html" + CR</pre>
These two settings are used to set the default HTTP header that Web Connection uses if you don't create a full custom header of your own. You can add any custom tags here, such as forced page expirations and they will be appended to every HTTP header that goes out. Note that if you make any changes to the header in your application code this default is not used. 



<pre>#DEFINE MAX_STRINGSIZE  			10000</pre>
Web Connection in many cases creates HTTP output as strings which are passed back to the Web server. But it's actually much faster writing data out to a temporary file and then reading it back in rather than building strings by concatenation once strings get lengthy. The value above determines when Web Connection starts dumping strings to file rather than keep concatenating them. 10000 bytes is about the break even point where file processing becomes faster. Larger numbers mean less file buffering and larger memory buffers. Faster machines do better with bigger buffers. If you have a real old machine you might benefit by reducing the string size to 5000 (which was optimal for P2 machines at the time).

<pre>
#DEFINE WWC_SERVER				wwServer
#DEFINE WWC_SERVERFORM 			wwServerForm
#DEFINE WWC_SERVERFORM_VFPFRAME wwServerFormVFPFrame

#DEFINE WWC_PROCESS         	wwProcess
#DEFINE WWC_WEBSERVICE			wwWebService

#DEFINE WWC_SESSION 			wwSession
#DEFINE WWC_SQLSESSION 			wwSessionSQL

#DEFINE WWC_REQUEST				wwRequest
#DEFINE WWC_REQUESTASP			wwASPRequest

#DEFINE WWC_RESPONSE			wwResponse
#DEFINE WWC_RESPONSEFILE    	wwResponseFile
#DEFINE WWC_RESPONSESTRING		wwResponseString
#DEFINE WWC_RESPONSEASP			wwASPResponse
#DEFINE WWC_RESPONSEBEHAVIOR 	wwResponseFileBehavior
#DEFINE WWC_HTTPHEADER			wwHTTPHeader

#DEFINE WWC_wwEval 				wwEval
#DEFINE WWC_wwHTMLControl 		wwHTMLControl
#DEFINE WWC_WWVFPSCRIPT     	wwVFPScript
#DEFINE WWC_WWPDF				wwPDF
#DEFINE WWC_WWSOAP				wwSOAP
</pre>
This group holds all of the framework classnames. Internally Web Connection never references any class names explicitly but instead creates classes using these #DEFINEs. This allows you to subclass the framework classes and replace the classes here with yours to get your functionality into the framework. 


<pre>#DEFINE WWC_LOAD_DYNAMICHTML_FORMRENDERING  .T.
#DEFINE WWC_LOAD_WWSESSION 					.T.
#DEFINE WWC_LOAD_WWBANNER 					.T.
#DEFINE WWC_LOAD_WWDBFPOPUP 				.T.
#DEFINE WWC_LOAD_WWIPSTUFF 					.T.
#DEFINE WWC_LOAD_WWVFPSCRIPT 				.T.
#DEFINE WWC_LOAD_WWSQL						.T.
#DEFINE WWC_LOAD_WWPDF						.T.
#DEFINE WWC_LOAD_WWXML						.T.  && Don't change! Required!
#DEFINE WWC_LOAD_WWMSMQ						.F.
#DEFINE WWC_LOAD_WWSOAP						.T.
</pre>
This set of flags is checked when Web Connection loads. Basically, these are flags that determine whether some of the support classes are loaded on startup. This is here mainly to keep down project size by excluding classes you don't use. Do not remove the wwXML component - it's required and here only for backwards compatibility.

The remainder of settings in wconnect.h are system defines and values that are used internally in various classes. 

Additional useful flags:

**#DEFINE WWC_CACHE_TEMPLATES			0**  
Determines whether templates called with Response.ExpandTemplate()  are cached in a table rather than being read from disk each time. If you have applications that use lots of templates this approach may provide a significant performance boost. Number specifies the number of seconds that a template is cached - 0 means that no caching occurs.

**#DEFINE WWC_EXTENDED_LOGGING_FORMAT  .F.**  
This option when set to .T. causes additional information to be logged into the Web Connection Request log. When set, the POST data and Browser string get logged in addition to the querystring, script, and client IP address. Turning this option on can quickly generate a very large log file so please use this option with caution and if you do use frequently clean out your log!

**#DEFINE MAX_TABLE_CELLS 			15000**  
Determines the number of table cells that the wwShowCursor class can render in a single table before changing output format to a text based list. Since tables can get too large to comfortably render as tables this maximum can be applied.