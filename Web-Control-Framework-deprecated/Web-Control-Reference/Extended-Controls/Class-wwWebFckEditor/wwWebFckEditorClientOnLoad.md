Optional client side event fired at the end of configuration but before the editor has loaded
Allows overriding and configuring any of FCKs options in the source page.

Using this mechanism gives you access to all of FCK's configuration objects in the current page by allowing you to simply create a function that handles any settings. 

Note the handler is fired **before** any of the control property settings are applied meaning that any properties set on the control override any settings you might make in the script handler code.

The ClientOnload handler receives two parameters Editor and Config which represent the Editor instance and the instance's Configuration object.

```cs
<script type="text/javascript">
function FckLoaded(Editor,Config)
{
   Config.StartupFocus = true;
   Config.ForcePasteAsPlainText = true;
   Config.TabSpace = 4;
}
</script>
```