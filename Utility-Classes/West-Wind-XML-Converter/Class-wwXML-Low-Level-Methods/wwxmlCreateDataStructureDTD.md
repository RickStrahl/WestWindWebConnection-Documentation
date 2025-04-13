This method creates a DTD from the currently active Alias's data structure. The DTD generated is not complete in that it lacks the DocType and top level document root definition. This is so you can string together multiple cursors or objects in a single DTD.

The generated DTD elements look like this for a table:

<pre><!ELEMENT wwbanners (row)*>
<!ELEMENT row (bannerid,image,link,redirect,imgtags,order,type,hits,maxhits,clicks,test,test2)>
<!ELEMENT bannerid (#PCDATA)>
<!ATTLIST bannerid
          type CDATA #FIXED "string"
          size CDATA #FIXED "8"
>

... more elements here

<!ELEMENT image (#PCDATA)>
<!ATTLIST image
          type CDATA #FIXED "string"
          size CDATA #FIXED "50"
></pre>

Note that the DocType DTD header and the docroot element are missing as well as the DTD closing tags:

<pre><!DOCTYPE xdoc [
<!ELEMENT xdoc (wwbanners)>

... element code from above goes here

]>
</pre>

Note that this setup allows for maximum configuration at the expense of some ease of use. However, it's fairly easy to create the DTD header and footer with a single line of code. The following is actually what CursorToXML does:

<pre>IF THIS.lCreateDataStructure
   lcOutput = lcOutput + ;
               "<!DOCTYPE " + THIS.cDocRootName + " [" + CR + ;
               "<!ELEMENT " + THIS.cDocRootName + " (" + lcName + ")>" + CR + ;
               THIS.CreateDataStructureDTD(lcName, lcRowName) + CR + ;
               "]>" + CR + CR
               
ENDIF
</pre>

If you were to add additional tables or objects you'd have to add them to the lcName code above.