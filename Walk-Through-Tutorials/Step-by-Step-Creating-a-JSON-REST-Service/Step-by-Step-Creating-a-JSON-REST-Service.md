A common scenario for modern Web applications is to build REST API services that act as data services for front end applications. This maybe a Web based application running entirely on the front end using a client side framework like AngularJs, Aurelia, Ember or other framework. It might also be a device based application running on a mobile device in iOS, Android or Windows, or even a desktop application that simply loads its data over the Web.

Most REST services these days use **JSON (JavaScript Object Notation)** to communicate and the Web Connection [wwRestProcess class](VFPS://Topic/_3I012WDDT) lets you easily build REST services by simply implementing a class and adding methods that receive parameters and return result values, that are marshalled from and to and JSON. Unlike the 'standard' Web Connection HTTP handler implementation, a wwRESTProcess service has additional logic associated with it that knows how to de-serialize JSON data into a parameter, and serialize a result value into JSON.

There are two ways to create a new REST service:

* Create a new project
* Create a new Process Class and add to an existing project

To expand on the previous examples, we'll choose the latter approach and extend the existing project by creating a new Process class and use the business objects we created in [MVC and Business Object Tutorial](VFPS://Topic/_0I102WSAI). But you can also use a new project in the same way as outlined in [Getting Started Tutorial](VFPS://Topic/_0NB1AL6FM). Just use the **New Project Wizard** instead of the **New Process Wizard** we use in this tutorial and choose **REST API Service Process Class** in the last step of the Wizard.

> #### @icon-info-circle Online Sample Code
> You can find the sample code for the completed projects at the following GIT BitBucket repository:
>
> **<a href="https://bitbucket.org/RickStrahl/webconnection_stepbystep_samples" target="top">Web Connection Step By Step Samples on BitBucket</a>**
>
> You can clone the repository, or use <a href="https://bitbucket.org/RickStrahl/webconnection_stepbystep_samples/get/1ffadb1f224c.zip" target="top">Download Button</a> to grab a Zip file of the completed project folder.

{{ Helpers.ChildTopicsList() }}