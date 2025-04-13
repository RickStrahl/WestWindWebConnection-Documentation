The scripting class supports operation using Dynamic Compilation (FXP generated if script has changed), Precompiled FXP (only FXP files are executed) and Interactive with ExecScript (using ExecScript). Various other options exist to tailor the operation of the script execution for development and runtime situations.

```foxpro
DO wwScripting
SET SAFETY OFF  && so files can be created without overwrite prompts

*** Optional: Add any other libraries **your code** is using for HTML generation 
DO wwHtmlHelpers && not required used only if used in the scripts


loScript = CREATEOBJECT("wwScripting")
loScript.lSaveVfpSourceCode = .T. && Optional for demo so we can see the code
loScript.lEditErrors = .T. && Allow editing errors immediately in the Fox Editor (goes to line of code if possible)
loScript.lShowFullErrorInfo = .T.

*** Set the 'web root' path so `~/` paths can be resolved
loScript.cBasePath = "\webconnection\fox\web" && Better: FULLPATH(".\web")

*** Actually render to a string
lcHTML = loScript.RenderAspScript("C:\webconnection\Fox\Web\wcscripts\nocode.wcs")

IF loScript.lError
      ? loScript.cErrorMsg

      IF !ISNULL(loScript.oException)
         ? loScript.oException.LineContents
         ? loScript.oException.Details
         ? loScript.oException.LineNo
      ENDIF
ENDIF   

ShowHtml(lcHtml)
```

The parser essentially turns ASP style <%= %> expressions and <% %> Code blocks into a FoxPro PRG file that can be executed dynamically.

Here's an example of a script file:
```foxpro
<html>
<body>
Hello world. Time is: <%= DATETIME() %>
<hr />
<%
    lcOutput = ""
    for x = 1 to 5
        lcOutput = lcOutput + TRANS(x) + "<br>"
    endfor

    *** Write the result into the output stream
    Response.Write(lcOutput)
%>
Query Result:<hr />
<%
    SELECT Company from TT_CUST INTO CURSOR TQuery 
    SCAN
%>
Company: <%= Company %><br />
<% ENDSCAN %>
</body>
</html>
```

which is turned into this Visual FoxPro code:

```foxpro
PARAMETERS _out
_out = []
_out = _out + [<html>]+CHR(13)+CHR(10)
_out = _out + [<body>]+CHR(13)+CHR(10)
_out = _out + [Hello world. Time is: ]

_out = _out + TRANSFORM( EVALUATE([ DATETIME() ]) )
_out = _out + []+CHR(13)+CHR(10)
_out = _out + [<hr />]


    lcOutput = ""
    for x = 1 to 5
        lcOutput = lcOutput + TRANS(x) + "<br>"
    endfor

    *** Write the result into the output stream
    Response.Write(lcOutput)

_out = _out + []+CHR(13)+CHR(10)
_out = _out + []+CHR(13)+CHR(10)
_out = _out + [Query Result:<hr />]


    SELECT Company from TT_CUST INTO CURSOR TQuery 
    SCAN

_out = _out + []+CHR(13)+CHR(10)
_out = _out + [Company: ]

_out = _out + TRANSFORM( EVALUATE([ Company ]) )
_out = _out + [<br />]

 ENDSCAN 
_out = _out + []+CHR(13)+CHR(10)
_out = _out + [</body>]+CHR(13)+CHR(10)
_out = _out + [</html>]
```

The scripting class manages the translation of the script code into this foxpro code as well as compiling and executing of the code. A variety of options determine exactly how the execution performs.

### Error Handling
When errors occur during execution wwScripting will generate an error page and display the content of the error page instead of any HTML content. The error output is generated as HTML always. Alternately you can check the various error properties  (lError, cErrorMsg and the oException) for error information.

If lShowFullErrorInfo is set to .T. the error info includes a display of the source code surrounding the error, so it is easier to find the error. If lEditError is set to .t. wwScripting will open an editor window and attempt to highlight the error for you and allow you to fix the problem.