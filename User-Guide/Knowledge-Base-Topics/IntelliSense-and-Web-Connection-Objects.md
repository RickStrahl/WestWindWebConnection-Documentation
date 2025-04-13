It would be nice to get Intellisense on Web Connection objects wouldn't it? Unfortunately by default you don't get for the intrinsic objects such as Request, Response, Server etc. because these objects are scoped PRIVATE higher up the hierarchy.

There's a hack to work around this though: You can use an `#IF .F.` block to add `LOCAL` declarations for these objects into your methods like this:

```foxpro
FUNCTION WCDoSomething

#IF .F. 
LOCAL Request as wwRequest, Response as wwResponse
#ENDIF

* ** Now Intellisense works (assuming wwRequest is loaded in memory)
Response.Write(...)

ENDFUNC
```

You can take this a step further and use an Intellisense Script to create each new method with this code already embedded in it:

```foxpro
LPARAMETER oFoxCode
LOCAL lcCmdLine, lcClassName, lcFunctionName, lcOutput

IF VARTYPE(go_FoxCodeLastClass) = "C"
   lcDefaultClass = go_FoxCodeLastClass
ELSE
   lcDefaultClass = ""
ENDIF
  
lcClassName = INPUTBOX("Class Name","Class Definition",lcDefaultClass)
lcFunctionName = INPUTBOX("Function","Class Definition")

IF EMPTY(lcFunctionName) or EMPTY(lcClassName)
   RETURN lcCmdLine
ENDIF

* ** Save the class used
PUBLIC go_FoxCodeLastClass
go_FoxCodeLastClass = lcClassName

oFoxcode.valuetype = "V"

TEXT TO lcOutput TEXTMERGE NOSHOW
*****************************************************
* << lcClassName >> :: << lcFunctionName>>
*****************************************************
FUNCTION  &lt;&lt;lcFunctionName&gt;&gt;()

#IF .F. 
LOCAL Request as wwRequest, Response as wwResponse
#ENDIF
~

ENDFUNC
*  &lt;&lt;lcClassName&gt;&gt; :: &lt;&lt;lcFunctionName &gt;&gt;
ENDTEXT

RETURN lcOutput
```