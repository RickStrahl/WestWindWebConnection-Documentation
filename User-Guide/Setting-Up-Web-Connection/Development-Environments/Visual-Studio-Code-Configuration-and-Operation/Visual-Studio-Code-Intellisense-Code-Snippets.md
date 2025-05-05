Web Connection installs a set of code snippets into Visual Studio code which are accessible via the `wc-` prefix in the editor. If installed you should see something like this:

![](/images/misc/vscodeintellisensesnippets.png)

Snippets vary in complexity from very simple static text expansions to more complex snippets that ask for input for naming. There are snippets that create entire HTML documents and mimic the full Visual Studio Page templates (use `wc-template-`).

You can check out the code snippets which live in this location:

```
%appuser%\Code\User\Snippets\wc-webconnection-vscode.code-snippets
```

You can open this file as a `JSON` document in VS Code and see what each of the snippets does. As you can see code snippets are relatively easy to create and you can create your own snippets in a separate file.

Here are a few snipppets of interest:

* **wc-tmp-**  
These snippets stand templates that generate entire pages and are similar to the full Visual Studio Page templates. You can create a Script Content Page, a full self-contained Script page, a Layout Page and a standalone HTML Page all using the standard Web Connection styling.

* **wc-html-**  
These snippets insert various Web Connection [wwHtmlHelpers](VFPS://Topic/_3361EQOP8) like textboxes, dropdowns, labels, date time pickers, binding errors, error dialogs and so on.

* **BootStrap Snippets**  
There are a number of snippets that generate common Bootstrap constructs like  `wc-input-group`, `wc-form-group`, `wc-button-group`, `wc-menu-dropdown-button` and so on. It's not all that comprehensive but I find I used these alot in the samples.


### Manually installing the VS Code Snippets
If you install VS Code after you install Web Connection, you'll have to manually copy the `code-snippet` file to the appropriate folder.

From a Windows Command Prompt:

```dos
cd "\wconnect\VisualStudio\Visual Studio Code CodeSnippets"
ROBOCOPY *.code-snippets  "%appdata%\Code\User\Snippets"
```

There's a `CopySnippets.bat` file in that folder that you can run as well.