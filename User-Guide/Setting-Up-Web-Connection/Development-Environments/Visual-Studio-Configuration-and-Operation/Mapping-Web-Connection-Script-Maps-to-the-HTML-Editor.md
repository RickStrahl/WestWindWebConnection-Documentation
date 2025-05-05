By default Visual Studio only understands certain file types that it knows about. Web Connection tends to use custom file extensions for script files so for example the script map I use for my message board is `.wwt` for my blog it's `.blog`. If I open these script files in Visual Studio without mapping extensions, they are displayed as plain text without syntax coloring because Visual Studio has no idea that these files are HTML files.

In order to make these files show HTML syntax coloring I can add them by going to **Tools -> Options -> Text Editor -> File Extension** which brings you to this dialog:

![](/images/misc/VisualStudioFileExtensionsOptions.png)

Here you can map an extension to one of the many editors that are available in Visual Studio. For Scripts and Templates you generally want the **Html Editor**. 

Type your extension name, then select the **Html Editor** from the dropdown list and click **Apply**. Your mapping is now ready.

Note that any already open documents have to be re-opened to see the changed association.

### Troubleshooting
*Problem: I'm trying to edit a Web Connection Script or Web Control Page and I see no syntax coloring for my page. What can I do?*

Visual Studio has an annoying issue that sometimes it'll loose the association between the Web Connection template extension and the Visual Studio custom editor associated with the extension. The result is that you see a plain text page without syntax coloring.

In orderto fix this issue you'll need to recreate the editor mapping by going back into the **Tools -> Options -> Text Editor -> File Extension**  dialog.

#### If your Extension is not listed  
If your script map extension is not in the list, add it again:

* Type the extension into the Extension text box
* For Scripts and Templates pick **Html Editor** from the drop down
* Click **Apply**
* Click Ok on the dialog

Go back to your project and close any files that are open with this new extension, then re-open them. You should now have syntax coloring.

#### If your Extension is listed
If the extension is listed and syntax coloring is still not working, it means that Visual Studio has corrupted the setting and you need to remove it and re-add it. 

Make sure to follow these exacts steps - they are important even if they seem redundant:

Select your extension in the list
* Select your extension
* Click *Remove*
* Now type the extension name
* Select *Html Editor* for script and template extensions
* Click the *Add* button
* Click Ok on the dialog

It seems unintuitive to have to first remove and then re-add, but just editing the extension is not sufficient. It has to be removed and re-added to work.

### Design Time Only
To be clear, these editor settings are design time only settings that control operation in Visual Studio and they have absolutely no effect on the application running at runtime.