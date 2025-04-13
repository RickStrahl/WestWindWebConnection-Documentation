If you're using Web Connection in MVC mode and create HTML scripts or templates, you'll want to set up custom editor associations for your script pages. For example, in wwThreads I want to edit my `Thread.wwt` page and have it treated like HTML:

![](IMAGES/misc/VSCODEFOLDERMODEOPENED.PNG)

Notice that the syntax used is displayed on the toolbar on the status bar on the right. 

You can click on the language to select a different language. There you can change the language just once by selecting the syntax, or you can select a permanent association using **Configure File Association for .wwt** where `.wwt` is my custom script map for the Web Connection application. If you click this option you get to chose a syntax and you should select **HTML**.

And voila, once you do you should now see all of your templates with that extension display as properly syntax colored HTML.