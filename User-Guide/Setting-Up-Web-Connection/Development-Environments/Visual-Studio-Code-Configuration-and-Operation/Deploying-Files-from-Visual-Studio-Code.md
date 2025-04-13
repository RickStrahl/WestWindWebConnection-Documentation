If you are working with Web resources in Visual Studio code you might want to deploy some of your changed files to a Web server. This is one feature that **the full Visual Studio** inetegrates beautifully natively, but you can also use some of this functionality in Visual Studio Code.

### The Deploy Addin
The [Deploy Addin for VS Code](https://marketplace.visualstudio.com/items?itemName=mkloubert.vs-deploy) lets you upload individual files to a server via FTP and a variety of other protocols. You can install this addin from the VS Code Addin sidebar - just search for `Deploy`.

Once installed and configured, you can then use `Ctrl-Shift-P` and choose `Deploy File` to upload the currently active or selected file to your server.

#### Configuration
To configure the Addin you need to add configuration options to the `settings.json` file for your local project folder.

Put the following file into the following location at your project root:

```
\.vscode\settings.json
```

and put the following json configuration into it:

```json
{    
    "deploy": {
        "targets": [
            {
                "type": "ftp",
                "name": "Publish wwThreads",
                "description": "Publish WebDemo Site",
                "dir": "/site_root/WebConnectionProjects/WebDemo",
                "host": "your-site.com",
                "engine": "ftp",
                "port": 21,                
                "secure": false,
                "user": "username",
                "password": ""  // leave blank: prompt
            }
        ]
    }
}
```

The `dir` will be your project root folder which should match the folder where VS Code has been opened. If you open VS Code with the Web Connection **Open Editor** command this will be the project root.

> **Make sure the root path matches your server path** or files may get copied to the wrong location on your server!

### Full Site Deployment
The Deploy Addin is a minimal addin and really only works for a file by file basis. It's great to update files while you're working.

If you need to deploy a full site you probably should use another tool:

* Full Visual Studio's Deploy Web Site is great for publishing and diffing sites
* [FileZilla](https://filezilla-project.org/) or other FTP Client