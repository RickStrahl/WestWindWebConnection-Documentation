High level method that imports XML into an object. The method can create the object or use an existing object to retrieve the object data. You can optionally pass in an existing object reference and the object will be updated from the XML data matching values of the matching element nodes by property name.

You can also generically import XML as long as the basic structure is followed:

<pre><xdoc>
   <objectname>
          <property1/>
          <property2/>
   </objectname>
</xdoc>
</pre>

The names of the elements is irrelevant, but the hierarchical document structure (xdoc/objectname/property) is.

If a datastructure DTD is included in the object, the object structure can be created on the fly, but only on flat objects that are not recursed. If ObjectToXML was used to export the object with a DTD the object is reassembled. All properties are rebuilt, but the actual object method interfaces are not available. To import to a complete object you must pass an object reference to this method.