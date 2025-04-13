Contains a collection of all the Tabs for this tab control. Based on the [wwWebTabPage class](vfps://Topic/_23V190JRD) which contains properties for Text, TabPageClientId, ActionLink, TabImage and Style.

The collection is of type wwNameValueCollection with the key being the ID of the page.

```foxpro
loTab = loPage.TabControl.TabPages.Item("Page1")  && or use Item(1) for the first page   
loTab.Text = "New Text"
```