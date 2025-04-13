This low level method imports a collection from an XML Node that follows a specific collection schema.

<pre>
<?xml version="1.0"?>
<xdoc>
	<class type="O" class="Empty">
<font color="Red">		<addresses type="O" class="Collection">
			<count>2</count>
			<keysort>0</keysort>
			<items>
				<item key="address1" type="O" class="Address">
					<caddress>400 Hexham</caddress>
					<cname>Markus Egger</cname>
				</item>
				<item key="address2" type="O" class="Address">
					<caddress></caddress>
					<cname></cname>
				</item>
			</items>
		</addresses></font>
		<objecttype>CollectionTest</objecttype>
	</class>
</xdoc>
</pre>

The import supports only simple types (can be mixed) or a single object type which must be provided by the first item in the collection passed to import to.