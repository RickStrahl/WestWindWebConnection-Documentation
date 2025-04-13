This method creates an error XML fragment. The fragment is not a complete document as it lacks the XML header, but rather is meant as a fragment that can be embedded into a full XML document.

**XML Format:**  
<pre><error>
   <errormessage>Couldn't open file</errormessage>
   <errornumber>-1</errornumber>
</error>
</pre>