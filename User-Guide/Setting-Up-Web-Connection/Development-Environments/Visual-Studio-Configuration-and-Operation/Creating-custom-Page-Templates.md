Web Connection ships with several custom Visual Studio page templates, which make quick work of creating new Web Connection Script and HTML pages. The templates are accessible via **File -> 

custom Visual Studio template that is used to create new Web Conection Web Control pages. This page contains everything to set up the page so you can simply add your FoxPro class name and start adding control to the main page. 

The default generated page templated looks like this:

```html
<!-- 
     * Set the name of your class in the ID property
     * Set the GeneratedSourceFile at a PRG file in your FoxPro project directory
     * NOTE: the path is relative to your executing directory (CURDIR())
     * Remove this block of comment text
-->
<%@ Page Language="C#" 
	ID="TEstPage_Page"
	GeneratedSourceFile="*** Path\TEstPage_Page.prg"
%>
<%@ Register Assembly="WebConnectionWebControls" 
    Namespace="Westwind.WebConnection.WebControls"
    TagPrefix="ww" 
%>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <title></title>
    <link href="westwind.css" rel="stylesheet" type="text/css" />
</head>
<body style="margin-top:0px;margin-left:0px">
    <form id="form1" runat="server">
    <ww:wwWebErrorDisplay runat="server" id="ErrorDisplay" />

    </form>
</body>
</html>
```

### How Page Templates work in Visual Studio
Page templates in Visual Studio are stored in your personal template folder and they are contained in a ZIP file that contains the template, a configuration file and an icon. The template zip files live in:

**c:\Users\<yourAccount>\Documents\Visual Studio 2005\Templates\ItemTemplates\Visual Web Developer**  

The easiest way to extend the template is to copy the Web Connection Page.Zip file and copy it to a new file say My Web Connection Page.zip.

![](IMAGES\WebControls\VsConfig\CustomTemplates.jpg)

Start by opening the default.wcsx page and customizing the template the way you'd like to. Maybe you want to add another or different style sheet, add an typical company logo or maybe even a common Web Control at the top and bottom of the page. Do whatever you think you need in your template for a default layout. Save the file.

Next open the MyTemplate.vsTemplate file which opens as an XML document in Visual Studio. Make a few changes like this to change the name and description and maybe the default file name for the template.

```xml
<VSTemplate Version="2.0.0" xmlns="http://schemas.microsoft.com/developer/vstemplate/2005" Type="Item">
  <TemplateData>
    <DefaultName>Default.wcsx</DefaultName>
    <Name>My custom Web Connection Page</Name>
    <Description>My Own West Wind Web Connection  Page</Description>
    <ProjectType>Web</ProjectType>
    <ProjectSubType>CSharp</ProjectSubType>
    <SortOrder>10</SortOrder>
    <Icon>__TemplateIcon.ico</Icon>
  </TemplateData>
  <TemplateContent>
    <References />
    <ProjectItem TargetFileName="$fileinputname$.$fileinputextension$" ReplaceParameters="true" >MyDefault.wcsx</ProjectItem>
  </TemplateContent>
</VSTemplate>
```

Save the file to disk. Now take those two modified files and copy them back into the My Web Connection Page.zip file and save the Zip file. 

The changes made are immediately available in Visual Studion - no need to even restart. If you open the Add New Item dialog in Visual Studio you should now see the My Web Connection Template in the selection list:

![](IMAGES\WebControls\VsConfig\CustomTemplates2.png)

It's easy to create custom templates - in fact it's so easy it might be useful to create a new template just for a specific project that loads all the project specifc stuff into a page.

If you create custom templates for yourself be sure you back them up so you don't loose them as you move across machines.