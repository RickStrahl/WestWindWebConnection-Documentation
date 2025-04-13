This high level method converts a live object reference to XML. All variables are converted to text and stored. 

Optionally can nest objects using the [lRecurseObjects](vfps://Topic/wwxml%3A%3AlRecurseObjects) property flag. You can also create an optional DTD for the object with the [lCreateDataStructure](vfps://Topic/wwxml%3A%3AnCreateDataStructure) property, but only if objects are *not* recursed multiple levels.

You can set the document root name (xdoc in the document) with the [cDocRoot](vfps://Topic/wwXML%3A%3AcDocRoot).