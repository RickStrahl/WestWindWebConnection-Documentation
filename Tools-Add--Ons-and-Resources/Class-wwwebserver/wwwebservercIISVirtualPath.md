IIS Metabase path for the Web site to work on if the site is IIS4/5 or later.

You can prompt for a list of all Web sites with the IISWebSites class:

<pre>SET CLASSLIB TO webserver addit

* ** PRIVATE Values set by Modal form
pcIISPath = ""
pcWebSite = ""
o = CREATE("IISWebSites")
o.Show()

* ** Values will contain selection
? pcIISPath
? pcWebSite
</pre>