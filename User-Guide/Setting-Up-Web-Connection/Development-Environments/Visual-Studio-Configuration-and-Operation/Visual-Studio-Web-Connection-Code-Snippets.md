Web Connection also ships with a set of Intellisense snippets for Visual Studio which help embedding common Web Connection directives and HTML/BootStrap constructs into HTML pages.

The snippets all have a `wc-` prefix.

### Installing the Snippets
Visual Studio supports adding new snippets via a special folder where Snippets are stored. Snippets are defined as XML files and those files have to be copied into a specific location in your Documents folder.

> Web Connection automatically installs the snippets when you run `Setup`. 

You can also manually install the Code Snippets by moving the snippets shipped into the appropriate Visual Studio Snippets folder.

#### Web Connection Snippets Folder

```txt
\wconnect\Visual Studio\Visual Studio CodeSnippets"
```

#### Visual Studio Snippets Folder

```txt
%userprofile%\Documents\Visual Studio 2017\ 
              Code Snippets\Visual Web Developer\
              My HTML Snippets\Web Connection
```

Simply copy the snippets from the Web Connection folder to the Visual Studio folder. You don't have to restart - snippets become immediately available.

### Using the Snippets
To use the snippets you can simply type any of the abbreviations of the snippets. The file names reflect the shortcuts for each of the snippets available, and if you're curious what snippets look like, you can peak at the XML. 

It's very easy to create snippets in an XML editor like VS Code (or Visual Studio), so you can easily create your own code snippets expansions.

Unfortunately Intellisense for custom code snippets in the HTML editor is spotty and doesn't show snippets reliably. However, there's an explicit Visual Studio hotkey `Ctrl-K-X` which brings up a navigation list of all installed Snippets. You can navigate to `My HTML Snippets\Web Connection` to find the Web Connection snippets available.

![](////images/visualstudiocodesnippetsctrlkx.png)