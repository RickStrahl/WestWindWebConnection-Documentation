### Calling a Web Service with a DataSet Result
```foxpro
LOCAL loSOAP as wwSOAP
loSOAP = CREATEOBJECT("wwSOAP")
loSoap.AddParameter("Name","")
loDOMDataSet = loSOAP.Callwsdlmethod("GetAuthors",lcWSDL)

IF USED("AuthorList")
   USE IN AUTHORLIST
ENDIF
   
LOCAL oXA as XMLAdapter
oXA = CREATEOBJECT("XMLAdapter")
oXA.LoadXml( loDomDataSet.Xml )
oXA.Tables[1].ToCursor(.f.,"AuthorList")

* ** If you want to update set buffering
SET MULTILOCKS ON
CURSORSETPROP("Buffering",5)

BROWSE
```

### Calling a Web Service with a DataSet Parameter (Update)
```foxpro
SELE Authors && Cursor with Changes

* ** Generate the DataSet XML
LOCAL oXA as XMLAdapter
oXA = CREATEOBJECT("XMLAdapter")
oXA.UTF8Encoded = .t.
oXA.AddTableSchema("Authors")
oXA.IsDiffgram = .T.
lcXML = ""
oXA.ToXML("lcXML",,.f.,.T.,.T.)

loDOM = CREATEOBJECT("MSXML2.DomDocument")
loDom.LoadXML(lcXML)

LOCAL loSOAP as wwSoap
loSOAP = CREATEOBJECT("wwSoap")
loSOAP.AddParameter("Ds",loDOM.DocumentElement.ChildNodes,"nodelist")
? loSOAP.CallWsdlmethod("UpdateAuthorData",lcWsdl)
```