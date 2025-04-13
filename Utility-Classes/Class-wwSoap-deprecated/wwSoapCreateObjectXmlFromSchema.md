Creates Xml from an object with proper case field names based on the WSDL schema definition of the Web Service.

This method needs to be used to create object graph XML IF the Web Service uses properties/fields in passed objects that require mixed or upper case field names. Since VFP cannot generate mixed case property names to be returned by AFIELDS() this method works around this by walking over the schema and pulling in properties based on the schema.

Note: You have to first call the ParseServiceWsdl method to cause the objects to get loaded:

```foxpro
loSoap.Parseservicewsdl(lcWSDL,.T.)
```

This method is used internally if you use AddParameter() and pass a WSDL custom type.