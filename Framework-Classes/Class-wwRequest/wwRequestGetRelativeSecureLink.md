This method allows you to easily create a secure link simply by specifying a relative link like any other link. 

The problem is that switching between HTTP and HTTPS requires a fully qualified URL including protocol, domain name and full Web path, while most sites entirely link with relative links where the protocol and domain name are never used explicitly. It's often difficult to derive the full URL for a secure link especially if the site must be portable and the link cannot be hardcoded with a domain name required to do this.

This method figures out the full current URL converts it to a fully quallified secure link and strips of the document name, then add your link or path at the end. So now to switch you can simply do:

<pre><a href="<%= Request.GetRelativeSecureLink([Somepage.wc]) %>">Secure Page</a></pre>