Retrieves a multipart form variable from the request buffer. Multipart form variables are submitted on the client side by specifiying an encoding type of "multipart/form-data".

Multipart forms are inherently more efficient especially for large form submissions since the data is not encoded. Multi-part forms are also used for HTTP file uploads.

To create a multipart form add the following enctype attribute to your form:

<pre><form ACTION="wc.dll?wwDemo~FileUpload" METHOD="POST" enctype="multipart/form-data">
     <font face="Verdana"><input TYPE="FILE" NAME="File">
      <br>
      <strong>File Description:</strong><br>
      <textarea rows="4" name="txtFileNotes" cols="43"></textarea><br>
      <input TYPE="submit" value="Upload File" name="btnSubmit"> </font></font></p>
</form></pre>