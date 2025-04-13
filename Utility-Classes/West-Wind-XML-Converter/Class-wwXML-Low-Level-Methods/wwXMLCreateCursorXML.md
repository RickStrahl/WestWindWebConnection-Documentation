**Low Level Output Method**  

This method creates the actual XML body for cursor data. The data layout starts with the cursor level:

<pre><cursor>
   <row>
      <data1/>
      <data2/>
   </row>
</cursor></pre>

Use this method if you want to combine multiple objects/cursors into a single XML document, or if you want to create a very simple XML layout. This document is well formed, but requires at least the <?xml version="1.0"> header.