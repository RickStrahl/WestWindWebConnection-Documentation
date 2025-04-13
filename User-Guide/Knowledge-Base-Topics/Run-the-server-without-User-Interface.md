It's very easy to have your server's run invisibly on the desktop by setting the ShowServerForm key in the application's startup INI file (wcDemo.ini for example). 

### ShowServerForm=Off
You're effectively removing any UI from the server. If you plan on running your server as an InProcess COM component through MTS this setting will be automatically enforced as the server loads. The property on wwServer is lShowServerForm.

Why bother? First of VFP forms are notoriously memory hungry and even the small window increases VFP's resource use by almost a meg as VFP has to load the form engine. 

Second performance - the new wwServer class is no longer based on a form but on the RELATION class which is much more light-weight. The display update mechanism in WC 3.0 is actually calling into the form object via a method call. In version 2.x the display was simply updated - the current version that updates the display is slightly less efficient because of the indirect object access. Performance is nothing to loose sleep over with this, but if your app needs every ounce of horsepower to process hits, there's no reason to waste it on the UI. On my machine which is a P200 Notebook turning off the form results in a savings of between 1-2 hundreds of a second per request. Considering that the fastest request runs in .03 seconds this is not insignificant.

> #### Note
> Even with the form off the logging functionality still continues so if you need to check up on your server the Admin page's status lets you see whether the server's still running.