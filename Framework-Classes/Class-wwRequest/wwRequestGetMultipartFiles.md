Retrieves a collection of files that match the given form key. This method supports receiving a single file  or multiple files from an `< input type="file" multiple  />` form control or from an AJAX upload.

This method can support both classic style Server based uploads:

```html
<form action="FileUpload.wwd" method="post" enctype="multipart/form-data">
    <div class="fileUpload btn btn-primary">
            <span>
                <i class="fa fa-upload"></i>
                Select file to upload
            </span>
            
            <input type="file" id="upload" name="upload" 
                   class="upload" multiple accept="image/*" />
                   
        </div>
        <div id="filename"></div>
        <hr />
        <button type="submit" class="btn btn-primary"> Go</button>
    </div>
</form>
```

or using AJAX style uploads:

```html
<div class="container" style="padding-bottom:20px; border-bottom: solid 1px silver">
    <h3>Ajax File Upload</h3>
    <form id="ajaxForm" name="ajaxForm">
        <div class="fileUpload btn btn-primary">
            <span>
                <i class="fa fa-upload"></i>
                Upload Image
                <i id="ajaxProgress" class="fa fa-spinner fa-spin" style="display: none"></i>
            </span>
            <input type="file" id="ajaxUpload" name="ajaxUpload"
                    class="upload" accept="image/*" multiple />
        </div>
    </form>
</div>
<script>
    // Catch the file selection
    var files = null;
    $("#ajaxUpload").change(function (event) {
        files = event.target.files;

        // no further DOM processing
        event.stopPropagation();
        event.preventDefault();

        // show spinner
        $("#ajaxProgress").show();

        uploadFiles("Upload",
            "FileUpload.wwd?type=ajax",
            function uploadSuccess(data, textStatus, jqXHR) {
                var files = data;                            

                $("#ajaxProgress").hide();

                if (data.error)
                    toastr.error("Upload failed");

                toastr.success("Upload completed.");                            
                            
                var images = "";
                for (var i = 0; i < files.length; i++) {
                    var file = files[i];                            
                    images += '<img src="../temp/' + file.filename + "' />"
                }
                $("#ImageList").html(images);
            },
            function uploadError(jqXHR, textStatus, errorThrown) {
                $("#ajaxProgress").hide();
                toastr.error("Upload failed.");
            });

    });

    /*
        Ajax Upload function

        Pass in from Change event of file upload control

        fileId  -  Id of the file Form vars
                    file results in (file0, file1, file2 etc)
        url     -  Url to upload to
        success -  success handler
                    data object of JSON result data from server
        error   -  error handler


    */
    function uploadFiles(fileId, uploadUrl, success, error) {
        // Create a formdata object and add the files
        var data = new FormData();
        $.each(files, function (key, value) {
            data.append(fileId, value);
        });

        $.ajax({
            url: uploadUrl,
            type: 'POST',
            data: data,
            cache: false,
            dataType: 'json',
            processData: false, // Don't process the files
            contentType: false, // Set content type to false!
            success: success,
            error: error

        });
    }
</script>
```