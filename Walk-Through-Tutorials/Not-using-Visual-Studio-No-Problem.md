In these tutorials I use Visual Studio to demonstrate a number of Web Connection and JavaScript features. I prefer using Visual Studio for its many, powerful Intellisense features it provides for HTML, CSS and JavaScript which frankly is much better than any other tool out there. 

### Visual Studio Community is Free
And now **<a href="https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx" target="top"> Visual Studio Community Edition</a>** is a **free** version of Visual Studio Pro. It is **free** for all but bigger Enterprise customers (over 250 PC and over 1M in revenue).

### Visual Studio is not Required 
But - many people do not like Visual Studio because it's a monstrously big environment that takes gigabytes to install and takes a fairly powerful machine to execute effectively.

### Visual Studio Code
You can also use Visual Studio code and Web Connection provides a set of templates for this editor.

### Use any Html Editor
None of what I show requires Visual Studio. You can use **any editor** you feel comfortable with to create your applications.

There are many great editors out there for editing HTML, CSS and JavaScript. Here are a few that I've used and like:

* **<a href="https://code.visualstudio.com/" target="top">Visual Studio Code</a>**
* **<a href="https://www.jetbrains.com/webstorm/" target="top">WebStorm</a>** (for pay)
* **<a href="https://www.sublimetext.com/3" target="top">Sublime Text</a>** (for pay)
* **<a href="https://atom.io/" target="top">Atom Editor</a>**

I really like <a href="https://code.visualstudio.com" target="top">Visual Studio Code</a> as it has <a href="https://github.com/mattslay/visual-foxpro-language-template-for-visual-studio-code" target="top">an optional FoxPro syntax coloring plug-in</a> (created by Matt Slay) which lets you effectively edit FoxPro code.

* **<a href="https://code.visualstudio.com" target="top">Visual Studio Code</a>**
* **<a href="https://github.com/mattslay/visual-foxpro-language-template-for-visual-studio-code" target="top">Visual Studio Code FoxPro Sytnax Addin</a> (by <a href="https://twitter.com/mattslay" target="top">Matt Slay</a>)**

### Using Page Templates
One advantage of using Visual Studio is that Web Connection ships a number of default Page Templates that create a predefined HTML layout for you that you can quickly adapt. These are especially useful if you use Script or Template pages with a master Layout page:

![](/images/stepbystep/CustomerDemoAngularProjectDialog.png)

If you are not using Visual Studio you can still use these templates, but it'll be a little bit more work.

The base packages for each of the templates are provided in the following folder:

**C:\wconnect\Fox\VisualStudio\PageTemplates**

You can copy the `default.xxx` (and possibly `default.js`) files out of any of the template folders and copy them to your project with the new page name. 

For example:

```dos
copy "C:\wconnect\Fox\VisualStudio\PageTemplates\Web Connection Template Page\default.wc"
     "C:\webconnectionprojects\MyProject\MyPage.wcs"
```   

##### Replacing $safeitemname$
Most of the templates include a template expansion value `$safeitemname$` inside of them which corresponds to the base file name when the template is created. In Visual Studio this value is replaced with the filename without the extension.

If you're not using Visual Studio you'll want to replace this with the title of your page using the search and replace functions.