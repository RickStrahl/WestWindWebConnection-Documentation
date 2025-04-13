Retrieves a single file uploaded with HTTP file upload into a binary string.

> #### @icon-info-circle Use GetMultipartFiles instead
> We recommend you use [GetMultipartFiles](VFPS://Topic/_4H80RRA9J) instead of this function to allow retrieving multi-file selections which are common these days.

HTTP file uploads and multipart forms are sent up to server with multipart forms which are submitted in an HTML page as follows:
```html
<form action="FileUpload.wwd" method="POST" **enctype="multipart/form-data"**>
     <input type="file"  name="File" id="File">
      <strong>File Description:</strong>
      <textarea rows="4" name="txtFileNotes" cols="43"></textarea>
      <input type="submit" value="Upload File" name="btnSubmit">
</form>
```