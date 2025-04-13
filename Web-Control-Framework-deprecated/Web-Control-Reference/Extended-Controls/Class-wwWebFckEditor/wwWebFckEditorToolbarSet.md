The toolbar layout to display: Default - Basic

FCK Editor ships with the above ToolbarSets, but you can customize these by overridding settings in the configuration files.

One way to override toolbar button configuration in your page is by defining an ClientOnload handler and then using the following code in it:

```cs
<script type="text/javascript">
function FckLoaded(Editor,Config)
{
   Config.ToolbarSets["Common"] = [
	['Source','DocProps','-','Preview','-','Templates'],
	['Cut','Copy','Paste','PasteText','PasteWord','-','Print','SpellCheck'],
	['Undo','Redo','-','Find','Replace','-','SelectAll','RemoveFormat'],	
	'/',
	['Bold','Italic','Underline','StrikeThrough','-','Subscript','Superscript'],
	['OrderedList','UnorderedList','-','Outdent','Indent'],
	['JustifyLeft','JustifyCenter','JustifyRight','JustifyFull'],
	['Link','Unlink','Anchor'],
	['Image','Table','Rule','Smiley','SpecialChar'],
	'/',
	['Style','FontFormat','FontName','FontSize'],
	['TextColor','BGColor'],
	['FitWindow','-','About'] 
      ];

   Config.ToolbarSet = "Common";
}
</script>
```