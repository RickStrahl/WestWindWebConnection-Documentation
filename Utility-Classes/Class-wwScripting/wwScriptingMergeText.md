Provides Template parsing for expression evaluated tempaltes. Unlike script pages, MergeText evaluates pages by parsing each script block individually and executing the code contained in it. Uses EVALUATE for `<%= %>` expressions and EXECSCRIPT for `<% %>` blocks. 

Note that these pages do not execute as a single PRG file so individual Code blocks cannot see variables from other code blocks unless they are made PUBLIC. 

For expression based pages template parsing with MergeText can be significantly faster than the script functions.

```foxpro
DO wwScripting
SET SAFETY OFF

LOCAL loScript as wwScripting
loScript = CREATEOBJECT("wwScripting")

TEXT TO lcHtml NOSHOW
<html>
Hello world <%= DATETIME() %>

Dynamic Text:
<% lcOutput = ""
FOR x = 1 TO 10
	lcOutput = lcOutput + "Message" + TRANSFORM(x) + CHR(13) + CHR(10)
ENDFOR

RETURN lcOutput
%>

ENDTEXT
  
? loScript.MergeText(lcHtml)
```