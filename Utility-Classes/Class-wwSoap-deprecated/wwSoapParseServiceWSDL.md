Parses a WSDL document file into an object with an easily accessible structure.

**cInterface** - Name of the class
**cServerUrl**  - URL that handles this service
**aMethods[x]**  - Array of Method objects
**nCount** - Number of methods
**aTypes[x]** - Array of custom types for this service
**nTypeCount** - Number of custom types

aMethods consists of Method objects which have the following structure:

**cName** - Name of the Method
**aParameters** - Array of parameters  (2d: 1 - Name of parameter  2 - xsd type)
**nCount** - Count of parameters
**cResultName** - Name of the return value
**cResultType** - Type of the return value
**cSOAPAction** - The SOAP Action header for this method call
**cMethodNamespaceURI** - Namespace used for this method
**cMethodNameSpace** - The Namespace identifier

aTypes consists of all the custom types of the Web Service

**cName** - Name of the custome type
**nCount** - Number of custom properties
**aProperties** - Two dimensional array that contains each of the properties
1 - Name of the property
2 - XML type including namespace prefix (s:string for example)