This method is a simple way to create a fully self contained HTML page with just a couple of lines of code. This is great for testing output or error messages that let the user know of certain problems with the application.

This method also supports auto refresh of the Web page to go to another URL after a number seconds.

This method is implemented identical as the StandardPage method of this class. There are two separate versions to allow for two kinds of status pages to be displayed so error can look different than successful response pages. To customize these methods simply reimplement the methods in your subclass of wwProcess.

You can also override this method with a custom template by specifying a template file path in the [cErrorTemplate](vfps://Topic/wwProcess%3A%3AcErrorTemplate) property. When set the template is used instead of the hardcoded ErrorMsg method code.