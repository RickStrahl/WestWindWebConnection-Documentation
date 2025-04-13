Converts an XML type to a FoxPro type.

Based on the following logic:

```foxpro
DO CASE
   CASE lcXMLType $ "string,char,uri,uuid"
      lcType = "C"
   CASE lcXMLType $ "number,decimal,single,double,r4,r8,float,fixed.14.4,float.IEEE.754.32,float.IEEE.754.64" 
      lcType = "N"
   CASE lcXMLType $ "integer,i4,i1,i2,i8,ui2,ui4,ui8"
      lcType = "I"
   CASE lcXMLType = "boolean"
      lcType = "L"
   CASE lcXMLType = "object"
      lcType = "O"
   CASE lcXMLType $ "date,date.tz"
      lcType = "D"
   CASE lcXMLType $ "datetime,datetime.tz"
      lcType = "T"
   CASE lcXMLType = "record" 
      lcType = "O"
   CASE lcXMLType $ "base64Binary,bin.hex,base64binary"
      lcType = "B"  && Binary types may need different handling
   OTHERWISE
      lcType = "O"
ENDCASE
```