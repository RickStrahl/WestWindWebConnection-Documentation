Fixes up Urls based on an Application Relative path including support for ~ path syntax that injects the Application relative path into the URL.

When parsing the path, the ~ value is replaced with the Application's relative virtual path (ie. /wconnect/) which is appended to the Url provided replacing the ~. The application's relative path is retrieved from the app

For example:

Assume Web Connection is installed in /wconnect/ of the Web site and I want to reference an image in /wconnect/images/ while running the application out of /wconnect/weblog/.

To reference the image you can use:

~images/SomePic.gif

~ expands to the application's base path of /wconnect/ which is injected. 

ResolveUrl is used on all URL based properties in Web Conntrols. So the HyperLink control, the WebImage control all use this functionality to provide consistent pathing in your apps.

this.picImage.ImageUrl = "~images/wconnect.gif"