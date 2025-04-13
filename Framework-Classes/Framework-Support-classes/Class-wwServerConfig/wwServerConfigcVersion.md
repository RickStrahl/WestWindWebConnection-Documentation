A version property that can be set and used in scripts and other places to provide a unique id for the server. 

This is useful in an application to provide a unique version identifier that can be appended to URLs to guarantee that URLs for scripts and CSS files are reliably reloaded when the version changes. You can change this value either with the `<yourApp>.ini` configuration setting or via code in `OnLoad()` to assign a unique id. 

The value can be something like `1.0` or `1.32` or something completely random like `SUBSTR(SYS(2015),2))`.

### Cache Busting: Ensuring Resources reload even if Cached
A common use case is for cache busting which is to make sure that scripts and CSS resources that your site loads are reloaded when a new version is uploaded:

```html
<link href="./css/application.css?v=<%= Server.oConfig.cVersion %>" rel="css" />
<script src="./scripts/editor.js?v=<%= Server.oConfig.cVersion %>"></script>
```

Without this browser caches often cache these files and links for a long time, unless explicitly reloaded. By effectively creating a new unique URL with a version appended you are busting the cache resource and force it to reload and cache with the new versioned URL. This is a very useful feature and easy to implement simply by changing the version number.


### When to set the Version
You can set the version in:

* Your `<yourApp.ini>` file
* In Code during application startup


Using a configuration file is an easy way to set the value, but it may not be that convenient to actually update the configuration file on the server, especially if you have differing setting between client and server.

Another approach is to set the version during application startup, perhaps