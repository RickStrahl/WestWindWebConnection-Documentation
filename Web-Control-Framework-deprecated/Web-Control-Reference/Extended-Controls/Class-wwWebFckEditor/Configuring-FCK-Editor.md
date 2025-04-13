The wwWebFckEditor control provides basic configuration features for FCK Editor by setting the size and location on the page, allowing to preset the text value and set the various drop down lists. It's a basic and common set of settings, but it's only a very small subset of what's available in FCK Editor.

### FCK Editor JavaScript Configuration
FCK Editor is essentially a JavaScript API and as such you can use JavaScript to configure the editor. There are a number of ways this can be done. 

**fckConfig.js: The main Configuration File**  
First internally FCK uses a fckConfig.js file to set its default configuration settings. Go ahead and take a look at this file as it's a great reference of what configuration options are available.To make configuration changes you could change this file, but this is usually a bad idea because new versions will overwrite this file.

**CustomConfigurationPath property**  
Instead you can specify an override file. Using the wwWebFckEditor control you can specify the CustomConfigurationPath property which points at a separate virtual path to a .JS file of your own that overrides any of the settings in the fckConfig.js file. This allows changes to settings without breaking the original installation. A path is specified as a virtual such as ~/MyFckConfig.js or /config/MyFckConfig.js.

### ClientOnLoad JavaScript Hook
The control exposes an ClientOnLoad hook that allows executing some JavaScript on the client after all configuration has been done by the control, but before the control is actually displayed. This means this JavaScript handler and override configuration and editor settings. 

To do this start by setting the ClienOnLoad property to the name of a JavaScript function like say "FckLoaded".

Then create the script on the bottom above the </form> tag to make any override settings:

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

Note that the function receives two parameters - the Editor instance and the Config object instance both of which contain configuration settings.

Use the ClientOnLoad handler for page specific configuration changes. If there are a couple of setting you need to change this works fine. 

If you have many settings you need to change or you want to reuse the configuration changes in many pages or even across many applications make the change to the CustomConfigurationPath overide JavaScript file instead. It helps reduces page size and makes for better reusability.