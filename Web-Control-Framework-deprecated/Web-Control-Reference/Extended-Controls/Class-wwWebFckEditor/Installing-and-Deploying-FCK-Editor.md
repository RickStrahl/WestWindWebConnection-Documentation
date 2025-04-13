Web Connection does not ship with the FCK Editor files due to licensing and size isssues, so you will need to download and install the editor on your own. This is easy enough to do. Go to:

http://www.fckEditor.net/

and download the latest version of the editor which is a Zip file. Open the Zip file and unzip the entire installation into any of your Web directories so you have fckEditor subdirectory underneath your Web root. If you have a WebControls virtual folder (ie. c:\inetpub\wwwroot\WebControls) it should look like this :

![](IMAGES%2FWebControls%2FwwWebfckEditorHierarchy.png)

The editor contains many related pieces in this folder hierarchy including javascript files, images and stylesheets, but the editor is self contained and knows how to find all of these components on its own as long as the directory exists.

That's it. Once the control you can use the wwWebFckEditor control to configure editor instances on your form.

### Pathing to the FCK Library
The easiest thing to do is to install a copy of FCKEditor in your Web virtual directory, but if you have many different applications that use FCK this might not be efficient. You can also set up the editor in a central path like directly under the Web Root.

When you use the wwWebFcKEditor control you can specify the location of the library using the EditorBasePath property.