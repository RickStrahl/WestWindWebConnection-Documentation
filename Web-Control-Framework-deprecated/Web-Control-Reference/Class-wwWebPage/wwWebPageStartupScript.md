A collection of Script blocks that are rendered at the bottom of the Form. These scripts are automatically run when the page loads. 

Scripts are added to the Page in the order they are added to the object.

```foxpro
lcScript = [window.setTimeout("window.location='newpage.wcsx';"),5000)]
this.Page.StartupScript.Add("PageReload",lcScript)
```