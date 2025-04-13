HTTPS is crucial these days and you should be running all of your sites using HTTPS. With free TLS certificates from [Lets Encrypt](https://letsencrypt.org/) there's no good excuse for not using TLS certificates on your Web site.

If you are running a site that has a secure TLS certificate you can automatically force all requests by using the <a href="https://www.iis.net/downloads/microsoft/url-rewrite" target="top">IIS UrlRewrite</a>. UrlRewrite works by rewriting a URL - in this case a non-HTTPS URL HTTPS one by redirecting to the secure location.

### UrlRewrite has to be installed
UrlRewrite is not a native IIS component but has to be installed seperately. There are a couple of ways to install it:

#### <a href="https://www.iis.net/downloads/microsoft/url-rewrite" target="top">Microsoft Platform Installer</a>
The Platform installer is the official way to install support modules and components to IIS. The link above lets you manually install the Platform Installer and select the UrlRewrite module. Once installed you can also use the command line to install specific components:

```
"C:\Program Files\Microsoft\Web Platform Installer\WebpiCmd-x64.exe" /install /Products:UrlRewrite2 /AcceptEULA /SuppressPostFinish
```

#### <a href="https://chocolatey.org/packages/UrlRewrite" target="top">Chocolatey</a>
Chocolatey is an awesome tool to install applications and tools on Windows through an online registry. Chocolatey lets you automate installations and quickly install and update applications. You can go to the <a href="https://chocolatey.org/" target="top">Chocolatey site</a> for install instruction.

Once installed you can install UrlRewrite like this:

```
choco install urlrewrite -y
```

### Setting up Auto-HTTPS Redirection 
Ok - once UrlRewrite is installed you can now apply it in your `web.config`. You can also use the IIS Admin Console, but it's easier to just paste the following snippet into your `web.config`:

```xml
<configuration>
    <system.webServer>
        <rewrite>
          <rules>
            <rule name="Redirect to HTTPS" stopProcessing="true">
              <match url="(.*)" />
              <conditions>
                <add input="{HTTPS}" pattern="^OFF$" />
              </conditions>
              <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" redirectType="SeeOther" />
            </rule>
          </rules>
        </rewrite>
    </system.webServer>
</configuration>    
```

Now anybody visits your site with an `http://yoursite.com` URL it will be automatically redirected to `https://yoursite.com`.

> #### @icon-info-circle Redirect to HTTP GET Urls
> Note UrlRewrite only rewrites to HTTP GET Urls. Any POST/PUT operations that send data in the content body lose the data. This generally shouldn't be a problem.

> #### @icon-warning Major Windows Updates can disable UrlRewrite
> On Windows 10 major Windows updates - the bi-yearly updates - end up reinstalling IIS if it is already installed and you may have to **repair** or **reinstall** the UrlRewrite module. Windows Server does not have this issue, but if after install your server fails with **500.19** errors check you might want to reinstall the Module.