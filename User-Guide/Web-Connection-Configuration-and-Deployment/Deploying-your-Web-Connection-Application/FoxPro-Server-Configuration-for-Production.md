Running your server in development and production is different and there are a few settings that you should change in your server in order to make sure it works well in production.

* `DebugMode=off`
* `UnattendedComMode=on`
* `LiveReloadEnabled=off`


All three of these settings can be configured as part of your `MyApp.ini` file.

> It's probably a good idea to make these changes on your development machine for testing purposes first, to see how these changes affect your application before compiling and publishing your application to a live server.

## Turn off DebugMode
During development you typically want to run your server in `DebugMode` which means that the generic error trapping that captures any untrapped errors is disabled. This means during development the server stops on the line of code where an error occurs so that you can easily see the error, fix it and restart.

For production systems  **you don't want this behavior**. Instead you want **the server to intercept any errors and display an error page** when an error occurs.

For production do this in `MyApp.ini`:

```ini
[Main]
DebugMode=off
```

## In COM Mode Turn UnAttendedComMode On
When running a production application you don't want to accidentally have your server get stopped by a pop up dialog. FoxPro by default will display dialogs for certain failures like data file not or some access denied errors, warnings and validations depending on your Environment settings. 

In COM mode there's a way to **prevent any FoxPro dialog from popping up** by using **UnAttendedComMode**. 

```ini
[Main]
UnAttendedComMode=On
```

> This setting uses FoxPro's `SYS(2335,0)` to turn on COM UnAttended mode or off with a value of `1`. This feature **only works in COM Mode** - in File Mode this setting has no effect, which is one reason you should always run your production servers in COM Mode.

## Turn off LiveReload
LiveReload is a support feature for development that lets you run your server and automatically refresh pages and your server if a template, script or even FoxPro code is changed. LiveReload has both a FoxPro server component and a Web interface component both of which should be disabled for production. Since you don't make individual file changes that require immediate reload of pages, there's no need for live reload on a production site.

For production do this in `MyApp.ini`:

```ini
[Main]
LiveReloadEnabled=off
```
You also need to turn this off in your `web.config` or `WebConnectionWebServerSettings.xml` configuration:

**web.config**

```xml
<add key="LiveReloadEnabled" value="False" />
```

**WebConnectionWebServerSettings.xml**

```xml
 <UseLiveReload>true</UseLiveReload>
```

You can also disable the Web settings on the `Administration.wc` Module Administration page by toggling the Live Reload switch.