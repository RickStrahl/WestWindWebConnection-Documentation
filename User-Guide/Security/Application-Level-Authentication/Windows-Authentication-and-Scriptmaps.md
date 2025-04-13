This mechanism allows you to use Windows Authentication based on file permissions on the physical disk. The permissions on the physical file of a page determine how Authentication occurs and who's allowed access to a page. This option requires that EVERY request in the virtual directory has a physical page on disk - any non-page backed requests will result in a 404 error.

In Web Connection the typical scenario for this is to use script map pages such as Web Control Framework pages or ExpandTemplate/ExpandScript pages. With pages on disk Windows will validate the file permissions against the user's permissions and validate or reject the user that way.

### Setting up script maps
The key setup setting for this option is to make sure that you create one or more script maps for your application and make sure that you check the 'Check that File Exists' option. This option enables Windows to authenticate the page file permissions.

Note that this is a non-default setting. Web Connection by default doesn't check this flag in order to allow non-page backed methods to be fired. Once the flag is set you MUST have a backing page or the request will fail outright.

> #### Tip
>If you must have both Page backed and non-Page backed requests in a single application create two scriptmaps and map them both to the same process class. Set up on to require the page, the other not.

When the flag is set IIS will check the directory permissions and provide authentication information. If the user is not anonymous and authenticates you can check the LOGON_USER server variable which can be retrieved with [Request.GetAuthenticatedUser()](vfps://Topic/_S850QHU4X).

In summary:
* Create a script map and make sure that *Check if File Exists* is enabled
* Make sure you create a file on disk that matches the request you're hitting or you'll get a 404 error
* On this request remove the anonymous user or set rights as you need them to be
* To read the user logged in use Request.GetAuthenticatedUser()


### Administration Requests
Windows Authentication also works against requests of the wc.dll, but you can't access the DLL directly to do this because you cannot remove anonymous rights from dll - it needs those because it may serve non authenticated  requests.

So, you need to use a script map for those requests as well.  it for admin uses. YOU HAVE TO USE A SCRIPT MAP WITH A FILE ON DISK OR Windows AUTH will not work.

Steps:<ul>
* Create a script map for .ADMIN (or whatever) and point at wc.dll
* Create a file called wc.Admin 
* Set permissions as needed 
* set wc.ini to ANY (or a specific account)
* Remove Basic Auth from the wc.Admin file or the Web root 

You don't have to create a separate scriptmap for administration - you can use the same one described above for the main application. 

At this point Authentication should work against any admin requests:

**http://localhost/wconnect/wc.Admin?_maintain~ShowRequest**  

Note that if you are on Localhost or an Intranet, the login may just happen automatically without a login login dialog popping up. To verify you're logged in go to the link above and look Logged in username on the bottom of the form.

<blockquote>**Note: **  
Windows Auth returns usernames usually as domainormachinename\account as opposed to just returning the Account like Basic Authentication does. So if you set permissions for specific accounts you might want to do use:

**AdminAccount=rstrahl,rasnotebook\rstrahl**  
</blockquote>