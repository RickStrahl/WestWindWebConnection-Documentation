The low level methods of this class make it possible to build custom XML and string together multiple objects and cursors into a single XML document. There are two sets of routines for both cursor and object creation imports consisting of to export and two import routines. Each routine deals with one individidual object block (the structure or the data) and can be exported and parsed individually.

**Output Methods**  
<ul>* CreateDataStructureXML
* CreateCursorXML
* CreateObjectStructureXML
* CreateObjectXML
* CreateArrayXML
* CreateCollectionXML
</ul>


**Input Methods**  
<ul>* ParseXMLToCursor
* BuildCursorFromXML
* ParseXMLToObject
* BuildObjectFromXML
* ParseXMLToCollection
* ParseXMLToArray
</ul>