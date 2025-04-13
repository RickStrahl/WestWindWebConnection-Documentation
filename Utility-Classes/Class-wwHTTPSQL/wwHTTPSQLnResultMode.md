Determines how cursors are returned from the object. 

0 -  VFP Cursor*
2 - XML (no cursor)

By default VFP cursor data is returned and the cResponseXML property is always set with the raw XML response from the Web server. Using XML mode only the cReponseXML property is set and a cursor is not created. Use this if you don't care about the result set or if you for some reason only need an XML result.