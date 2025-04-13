When running on IIS and most other Windows based Web servers, Windows provides file based security. Standard security practices are extended over the Web by IIS in that file permissions are validated by the Web server and authentication is requested when the user does not have rights to access a file. 

Anonymous Web users come in through the `IUSR_` account so any public content must allow `Read/Execute` access to this user.

The IIS Microsoft Management Console handles most of this for you automatically - when you set up a Web directory it automatically adds `IUSR_` into the access list with the file attributes for reading and script access that you provide through the virtual directory dialog. If you need to protect individual file, you can go into Explorer and remote the IUSR_ account and add special accounts as you see fit.


> #### @icon-warning Block off the Admin Folder
> `IUSR_` access is what you want for most of your Web site, but for the Administration interface **you want to make sure that only authenticated users** can access the administrative page. To do this please make sure you **remove the `IUSR_` account** from the Admin directory permissions to disallow access by non-authenticated users. 
>
> For more info please see: [Securing your Web application](VFPS://Topic/_0J40WN2LX).


### Keep non-Web data in an unmapped path
Web applications typically break into multiple parts:

Web pages, Application code (if it's extended via COM or plain Fox code) and Data. If you keep sensitive data on your Web server, make sure that the data is not accessible via a relative path over the Web. IIS restricts Web server based navigation to the configured Web root (root directory or virtual folder) for the application so Web visitors cannot access files below this root directly.

Ideally the data should reside in an off-limits area away from the Web site in an unmapped path. If the data can sit on another machine and be accessed over a non-TCP/IP network connection, you can just about eliminate your risk for data piracy (at the cost of overhead for the network access). Of course if you use a SQL Server, either locally or on a remote machine, this won't be an issue as the built-in security will control access to the data. The same goes for code, if you keep it on the same box as the server.

If you want to be really secure and you have worries about people hacking into your box (doesn't happen, but paranoia is common, ya?) you can even move data off to another machine and then lock that machine down by not allowing access to it via TCP/IP. Use a different protocol to get at the data - NetBios or IPX, which makes it impossible for Web users to access this machine.

Web Connection's default project structure sets up a `Web` folder at the same folder level as the `Deploy` and `Data` paths by default and these paths cannot be traversed directly using IIS's Web access.

Make sure you remove permissions for all low access users (IUSR, Users group typically) from the non-Web folders like `Deploy` or `Data`, but make sure you add/leave access for the user specified in the IIS Application Pool you configured for the IIS Idenity. By default this is the SYSTEM account, but you can change this account in the IIS Management Console to an account of your choice.