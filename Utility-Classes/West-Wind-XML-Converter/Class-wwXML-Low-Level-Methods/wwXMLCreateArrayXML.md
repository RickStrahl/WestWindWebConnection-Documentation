This method is used internally to parse object arrays. This low level method is specialized and only works with one dimensional arrays at this time. 

Low Level Method. The method returns only an XML fragment rather than a full XML document. You can add a root tag and XML header to complete the document for standalone arrays. Internally this is used to manage array properties that contain multiple objects for child hierarchies such as line items on an invoice.

With this it's possible to do one to many representations such as Invoice to LineItems where line items can be an array of Lineitem objects.

This method supports typing for array elements so that items can be stored and be retrieved with their type intact.

<pre><inventory>
	<inventory_item type="C">TEst String</inventory_item>
	<inventory_item type="N">10</inventory_item>
	<inventory_item type="L">1</inventory_item>
	<inventory_item type="T">2003-11-02T11:14:58</inventory_item>
</inventory></pre>