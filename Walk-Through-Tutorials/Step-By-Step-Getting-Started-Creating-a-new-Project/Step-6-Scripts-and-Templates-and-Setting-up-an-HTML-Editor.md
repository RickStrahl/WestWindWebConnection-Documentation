Ok, now that we know how to look at data, the next step is to edit and update data and save it to disk. But before we do that, let's look at externalizing the HTML into scripts or templates to make it easier to create HTML and edit it efficiently.

### Html Creation with Templates
Up to this point we've done everything in code including HTML creation, which is not really optimal. For simple things, and highly dynamic applications that drive the UI entire with data, writing HTML in code is fine. It's an option.

But as applications get more complex, maintaining HTML generation in code gets difficult. Code HTML has to be recompiled in order to update even a simple HTML change or even a spelling mistake. You also can't easily use a good HTML editor to edit your HTML, so you miss out on the many great editors and features like syntax coloring, Intellisense, auto-completion, reference links and more for HTML code.

To help with this Web Connection supports a couple of mechanisms for using external HTML files as templates that mix HTML and FoxPro code and expressions:

* **Templates**  
Templates are **evaluated** pages that can contain FoxPro expressions and simple code blocks that look like `<%= expression() %>` or `<%= model.Value %>`. Templates are lightweight and completely evaluated in memory at runtime. They work best for block content that can be evaluated one block expression block at a time. It works great for pre-generated model data and expressions that generate HTML into an HTML page.  Templates can be updated easily while the application is running without any concern for concurrency, as templates are **parsed and evaluated each and every time they are called**.

* **Scripts**  
Scripts are similar to templates, but because they are **compiled into full FoxPro programs and executed** they are more flexible on what FoxPro language features you can use. Because scripts are real FoxPro code you can create structured statements (IF,SCAN,DO WHILE,FOR etc.) that can wrap around HTML and/or expression blocks making for rich scripting syntax. Because scripts need to compile at runtime there are some concurrency issues when running multiple instances that may require Web Connection server restarts after script changes.

Templates and scripts use the same overall syntax, but because templates are evaluated there are some limitations to templates in terms of structure statement execution. To execute FoxPro Block commands like `IF`, `DO WHILE`, `SCAN`, `FOR` inside of templates you have to use scripts. For this reason the recommendation is that most applications should use scripts rather than templates and scripts are the default 'scripting mode' in Web Connection new projects.

Templates and scripts should be (but don't have to be) used in combination with a Process class method that provides a **model** to be used inside of the template. This makes for a classic **MVC Pattern** (Model, View, Controller). Scripts use `Response.ExpandScript()` to execute, while Templates use `Response.ExpandTemplate()` to execute.

### Setting up an HTML Editor
In Step 7, we'll be using external HTML files as scripts or templates and in order to work with scripts or templates, you'll want to use a good HTML editor. Modern HTML editors are text editors with support for syntax coloring, Intellisense, auto-completion, zen coding, dependency resolution and much more.

You can use any editor you want, but Web Connection explicitly supports two editors with Web Connection specific document templates and code snippets:

* Visual Studio 
* Visual Studio Code

For this topic the page level templates are the interesting ones as they help us create a new HTML page with all the required page dependencies in place.

> ##### @icon-info-circle Templates are Optional
> These templates and even the Web Connection specific layout and dependencies are entirely optional. You don't need to use any of these if you prefer to create your HTML from scratch. These templates provide Bootstrap and FontAwesome support, add jQuery and provide a simple base page template that supports responsive desktop and mobile sized browsers.

The page level templates automatically add the Web Connection JavaScript and CSS references and/or create the appropriate type of layout for a self-contained HTML pages, self contained Template pages, Layout pages or embedded Content pages. These snippets are when Web Connection is installed. If you install either of these editors after Web Connection, you can re-run the Web Connection setup to ensure the templates and snippets are added to these Visual Studio versions.

## [Visual Studio](https://visualstudio.microsoft.com/)
Visual Studio is a very rich developer IDE. It's also quite big, but it provides many features and has excellent HTML editing support. Newer versions of Visual Studio can install feature specific work loads and when you install you'll want to install the .NET Web payload for Web Connection. Visual Studio has a number of versions including the free Visual Studio Community - all of them will work for the features you need for working with Web Connection sites.

To start using Visual Studio, start by opening the project in Visual Studio:

* Start Visual Studio 2019 (Community, Professional, Enterprise)
* Use **File -> Open -> Web Site**
* Point at the project's `/web` Folder
* Save by using **File | Save Solution As | WebDemo.sln**

![](//images/stepbystep/OpenVisualStudioWebFolder.png)

This opens the project and you can now edit documents. To enable syntax highlighting for custom extensions you need to explicitly add the extensions in the options. The extensions we'll want to add are `.wc .wcs .wp` for this project:

To do this go to:

* Go to **Tools -> Options**
* Drill into **Text Editor -> File Extension**
* In the dialog add the `wp` extension and map to **HTML Editor**
* Press **Apply**
* If not already there, repeat for `wc` and `wcs` and any other extensions you might use

![](//images/stepbystep/VisualStudioAddExtension.png)

You can now use the rich HTML editor in Visual Studio to edit your Web Connection scripts and templates with full syntax coloring, Intellisense and autocomplete support.

To create the `EditCustomer.wp` page:

* Select the Web Site Project node
* Right click to bring up the context menu
* Click **Add -> Add New Item**
* In the dialog select Visual C#

![](//images/stepbystep/CreateEditCustomerTemplate.png)

Which opens the document in the editor with syntax coloring:

![](//images/stepbystep/OpenEditCustomerTemplate.png)

## [Visual Studio Code](https://code.visualstudio.com/) 
Visual Studio Code is a text editor from Microsoft and although it has Visual Studio in the name it's not anything like Visual Studio: It's small and light weight, with support for lots of file formats and tons of extensions to provide language/markup specific features. It's not as visual as Visual Studio - a lot of the interactions are through menus or 'command palettes'. But it's also very fast - both as an editor as well as navigating the UI. It's awesome as a general purpose editor, and it also works great with Web Connection.

Start by opening Visual Studio code and opening the **Project root folder**. I like to open the **entire Web Connection project** so I can edit both the Web and FoxPro files. 

> #### @icon-info-circle Visual Studio Code is the Default Editor
> Once VS Code is installed the easiest way to launch VS Code for your project is by clicking the **Edit** button on the Web Connection Server window. VS Code is the default editor configured by Web Connection and the **Edit** opens the project in VS Code. You can configure this button to use a different editor command in `WebDemo.ini`.

To open VS Code manually:  

* Start Visual Studio Code
* Go to **File -> Open Folder**
* Select the project root: `\WebConnectionProjects\WebDemo`

Then to create the `EditCustomer.wp` page:

* Find the `web` folder
* Use **File -> New File** or **Ctrl-N** to create a new file
* Type the file name into the textbox: `EditCustomer.wp`

The `.wp` extension will be unknown, so in order to get syntax coloring configure the extension as HTML the first time you use it.

* On the StatusBar on the right side find the Language Mode button/section
* Click and select **Configure File Association for .wp Files**
* Select **HTML** from the list

If you installed VS Code **before your Web Connection installation** Web Connection automatically added Code Snippet expansions you can use. If you installed VS Code after Web Connection you'll manually have to add these snippets. To do this:

* Open a command prompt and `cd \wconnect\VisualStudio\Visual Studio Code CodeSnippets`
* Run `copysnippets.bat`

This copies the Web Connection snippets into the Visual Studio code snippet folder.

With the code snippets available you should now be able to type `wc-` and see a bunch of template options.

For this walk-through the interesting snippets are the `wc-tmp-` snippets which contain the page level templates:

* `wc-tmp-page` - Self Contained Script/Template Page (what we'll use here)
* `wc-tmp-page-html` - Self Contained HTML Page (no script/template)
* `wc-tmp-layout` - A master page Layout template
* `wc-tmp-content` - A template content page that uses a Master page for layout

So when you use the `wc-tmp-page` template you get the following script page:

![](//images/stepbystep/VisualStudioCodeEditorOpen.png)

And with that we're ready adding and editing templates in the next topic.

---

[Step 7 - Capturing Form Data using Scripts and Templates](VFPS://Topic/_SAP14Y146)