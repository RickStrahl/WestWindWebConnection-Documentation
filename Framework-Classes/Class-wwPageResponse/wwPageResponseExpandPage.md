Executes a Web Control Framework page by providing a physical path to the page allowing for parsing, compiling and running the page.

This method can be used to call any Web Control Framework Page from a standard wwProcess method. This allows control in scenarios where a single set of pages serve multiple applications for example.

```foxpro
*** Process Method
FUNCTION DoHelloworld
   Response.ExpandPage("c:\sites\MyApp\Helloworld.wwf")
ENDFUNC
```

Note that you have to specify the physical path to the page and the page must exist. Web Connection parses out the path to the PRG file from the script page. 

Please note that as an alternative to ExpandPage() you can also call any already parsed PRG Page classes directly which has the same result:
```foxpro
*** Process Method
FUNCTION DoHelloworld
   DO HelloWorld_page.prg
ENDFUNC
```

This approach is more efficient in terms of execution, but it requires that the page is already pre-parsed and so will not detect changes to the markup unless manually compiled.