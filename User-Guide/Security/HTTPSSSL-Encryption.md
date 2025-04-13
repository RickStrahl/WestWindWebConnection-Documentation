SSL encryption is automatically supported by Web Connection. There's nothing special to do, once a certificate has been installed into the Web Server:

* Purchase or generate an SSL certificate
* Install the certificate in IIS
* Start using `https://` links

You can purchase certificates from a variety of vendors like DnSimple, DirectNic, Verisign, Thawte and many more. There are now also several **free** providers including <a href="https://letsencrypt.org/" target="top">Mozilla's LetsEncrypt</a> which provide shorter term (3 month) certificates for free.

For more info on how to generate a **LetsEncrypt** SSL certificate in a few minutes you can check out this blog post:

* [Using Let's Encrypt on Windows](https://weblog.west-wind.com/posts/2016/Feb/22/Using-Lets-Encrypt-with-IIS-on-Windows)

The **LetsEncrypt-Windows** tool will generate a new certificate and install the certificate all in one easy operation.

If you use purchased certificate, you have to install it in IIS
and you can use the IIS Administration tool to generate a new certificate request. <a href="http://weblog.west-wind.com/posts/2014/may/08/iis-ssl-certificate-renewal-pain" target="top">The steps are described in this blog post</a>.

### SSL in Web Connection
Once installed, HTTPS usage is very transparent to your application. All you have to do to run securely is change your links to `https://` instead of `http://`. 

You can check whether a request is running SSL using:

```foxpro
llIsSsl = Request.IsLinkSecure()
```

When the request data is captured everything looks just like a plain HTTP request. You can check for a secure link by checking the Server port with wwRequest::ServerVariables("SERVER_PORT") which by default is 443 for an HTTPS/SSL request. You can also use [wwRequest::IsLinkSecure(cPortnumber)](vfps://Topic/wwRequest%3A%3AIsLinkSecure) to check for a secure request.


### Automatically switching into HTTPS/SSL mode
If you have an application that must be accessed in SSL mode you can automatically switch users into SSL mode by redirecting the current URL into HTTPS. Use the following code in your Process method of your Process subclass:

```foxpro
IF Request.IsLinkSecure()
   THIS.oResponse.Redirect( THIS.oRequest.GetRelativeSecureLink(THIS.oRequest.GetCurrentUrl()) )
   RETURN   
ENDIF
```