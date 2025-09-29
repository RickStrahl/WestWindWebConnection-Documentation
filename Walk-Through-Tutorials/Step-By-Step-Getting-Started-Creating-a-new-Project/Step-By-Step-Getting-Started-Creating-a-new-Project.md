The step by step guide takes you through creating a new project and then creating several request handler methods that respond to Web requests. This guide is an introduction to some of the features of Web Connection and uses primarily FoxPro data language commands directly in code to access data and display it.

> #### @icon-info-circle Online Sample Code
> You can find the sample code for the completed projects at the following Github repository:
>
> **[WebConnection-WebDemo on GitHub](https://github.com/RickStrahl/WebConnection-WebDemo)**
>
> You can clone this repository, or grab a Zip file of the completed project folder so you can grab the HTML templates to avoid typing all the HTML and code manually. 


After you've worked through this guide we recommend you check out the [Step By Step with the wwBusiness Object](vfps://Topic/Core%20Step%20by%20Step%20with%20the%20wwBusiness%20Object) guide, which uses the wwBusiness object to handle data access. Besides using the business object and showing how to utilize a business object in a Web based application, it also shows a few more advanced techniques for generating HTML interfaces in your applications.  
### A Note on Web Servers
Web Connection works with three different Web servers, two of which are considered development Web servers:

* Full IIS <small>(used for production, optionally for dev)</small>
* IIS Express <small>(dev only)</small>
* Web Connection Web Server <small>(dev, optionally for production)</small>

For local development we recommend you use either IIS Express or the Web Connection Web Server both of which require installation (instructions in the [first topic](VFPS://Topic/_1LY004TAP)). 

Servers can be used interchangeably assuming they are installed and configured - it's easy to switch between them. Functionality between different server types is very, very close, but there can be very small differences. Always test your application on your final production server before taking it live.


{{ Helpers.ChildTopicsList() }}