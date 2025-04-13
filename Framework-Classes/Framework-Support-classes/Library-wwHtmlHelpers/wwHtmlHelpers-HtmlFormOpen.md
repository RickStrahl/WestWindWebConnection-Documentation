Creates an HTML form open element tag.

**Examples:**  
```foxpro
*** Default implementations creates self post back form
? HtmlFormOpen() && <form action="" method="POST">
? HtmlFormOpen("PostToPage.wwd","POST","","form1") && <form action="PostToPage.wwd" method="POST" id="form1">
```