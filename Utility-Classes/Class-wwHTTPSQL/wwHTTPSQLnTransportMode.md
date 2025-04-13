Determines how cursors are sent over the wire from the server side.

**0 - XML * (default if not specified) using wwXML 
1 - VFP 7 CURSORTOXML/XMLTOCURSOR
2 - EncodeDBF() encoded VFP cursors
**  
2 sends an EncodeDBF() encoded cursor, but only if a cursor is returned. All other responses (errors, non-cursor results, stored procedure values) still are returned as XML. Note that you cannot return both a return value and a cursor using this Transport mode - only a cursor or only a stored procedure value can be returned but not both at once. This does work with XML.