When you update Web Connection the Web Connection Visual Studio 2015 and later Add-in will not be updated and in fact when it tries to install shows an error indicating that it couldn't install. This is nothing to worry about, except that the add-in is not actually re-installed.

In order to update the add-in you have to first remove it and then add it back in:

In Visual Studio:

* Go to Tools | Extensions and Updates
* Find the Web Connection Add-in
* Click Uninstall

Then use Explorer:

* Go to c:\wconnect\VisualStudio
* Double-click the WebConnection-Addin.vsix
* Follow the install dialog