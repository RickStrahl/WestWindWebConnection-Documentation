This method loads an XML document into a Microsoft XMLDom object. The method returns an object reference to the XML object or NULL in the event of failure.

If the document contains parsing errors this method will fail. In this case NULL is returned and lError is set to True and cErrorMsg contains full error information about the error and its location.