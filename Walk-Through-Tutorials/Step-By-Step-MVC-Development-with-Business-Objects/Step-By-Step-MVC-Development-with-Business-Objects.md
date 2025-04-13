In the Getting Started guide, I've shown you how to use VFP's data language to create cursors and then display that data. This works well, but it's not really great application design and most applications do or should use business object for the data access. 

In this guide, we'll take a more disciplined approach and I'll demonstrate how to use business objects using the [wwBusinessObject class](VFPS://Topic/_0GZ17U8F4) to manage your business logic and using a Model, View, Controller (MVC) based HTML rendering approach that separates your code from your HTML markup by using Scripts and Templates.

> #### @icon-info-circle Online Sample Code
> You can find the sample code for the completed projects at the following GIT BitBucket repository:
>
> **<a href="https://bitbucket.org/RickStrahl/webconnection_stepbystep_samples" target="top">Web Connection Step By Step Samples on BitBucket</a>**
>
> You can clone the repository, or use <a href="https://bitbucket.org/RickStrahl/webconnection_stepbystep_samples/get/1ffadb1f224c.zip" target="top">Download Button</a> to grab a Zip file of the completed project folder.


{{ Helpers.ChildTopicsList() }}