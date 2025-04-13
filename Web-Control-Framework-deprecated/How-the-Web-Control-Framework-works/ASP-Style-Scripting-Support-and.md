The Web Control Framework also supports Expression and CodeBlock Expression parsing and execution.

### Expressions 
While controls should be the main mechanism for embedding dynamic content into the page you can also embed any FoxPro expression directly into code with:

```html
<%= FoxPro Expression %>
```

Any valid FoxPro expression can be used with <%= %> syntax including database fields, variables, object properties, UDF() or user functions, class method calls etc. Anything that can be evaluated can be embedded as an expression. Any non string values get TRANSFORM() applied to convert it to a string.

**Examples:**  

```html
<%= Version() %>
<%= TQuery.FieldName %>
<%= loObject.Property %>
<%= MyUdfFunction() %>
<%= loObject.MyMethod() %>
```

Expressions can be used in most places including page level code, child control code and repeatable components as they are evaluated as an injected control at runtime.

### Code Blocks
Expressions are very efficient, but often require external logic to set up the expression or external code to be called. If you'd rather embed code directly in the page you can do this as well by using Code Blocks. Code Blocks are arbitrary blocks of FoxPro code that can be executed in the page:

```foxpro
<%
    lcHtml = ""
    x = 0
    FOR x = 1 TO 10
        lcHtml = lcHtml +  TRANS(x)
    ENDFOR
%>
<%= lcHtml %>
```

Code Blocks can also be broken into multiple blocks with markup in between for structured statements like IF/ENDIF, SCAN/ENDSCAN, FOR/ENDFOR etc.

```html
<%  select * from tt_Cust into cursor  TQuery  order BY company  %>
      SCAN %>
   <div class="containercontent">
       <%= Careof %><br />
       <%= DisplayMemo(Address) %>
   </div>
   <ww:wwWebTextBox runat="server" ID="txtEntered" ControlSource="Entered" />
   <hr />
            
<% 
* ** Rebind Theme txtEntered field to new record
this.txtEntered.DataBind()
ENDSCAN 
%>
```

Note that CodeBlock usage causes Web Connection to create a custom Render() method for your generated Page class. This Render() method includes the raw code of the script block. CodeBlocks are generated as FoxPro code and so execute at FoxPro machine speed - they are fast and relatively efficient.


> #### @icon-warning Code Blocks don't work in Child Controls
> Due to the way code blocks are 'injected' into Page and UserControl code by taking over the Render() method's output generation, <% %> blocks only work in Page and User Control code, **not in any child controls**. For example, you can't use <% %> blocks inside of a wwWebPanel control or a wwWebRepeater. This limits the usefulness of Code Blocks somewhat, but they are a convenience feature. Ideally your code should stay inside of the CodeBehind class.


**Generating Output from CodeBlocks**  
Sometimes it's useful to use code blocks to create a string and then output the resulting value. Unlike ExpandScript() style scripting Response.Write() will not generate output at the current location as output from Web Pages is cached in memory and written to the Response object only at the end of processing. Any call to Response.Write from within a page will result in the output being written at the very top of the page.

So, if you want to create output from within code you need to render the output into a string variable and then use an expression to render it. The following code demonstrates the process:

```foxpro
<%
       lcHTML = ""
       for x=1 to 10
           lcHtml = lcHtml + TRANS(x)      
       endfor                     
%>       
Result: <%= lcHtml %>
```

**Take care with Nested Script Blocks and Control Definitions**  
You should be careful when embedding Web Control Framework controls inside of CodeBlock loops. For example:

```html
<% SCAN %>
   Name: <ww:wwWebTextBox id="txtName" runat="server" ControlSource="Tquery.Name" />
   
<% 
    this.txtName.DataBind()
    ENDSCAN 
%>
```

Here a SCAN loop is used to create a number of wwWebTextBox controls. Each of the textbox controls created in this iteration is created with the **SAME ID**. This may or may not be a problem for your code. For read-only controls the above might be OK, but if you plan on reading data back from input controls or manipulate the controls via JavaScript code the non-unique IDs are likely to cause problems.

If you're using loops like the above it's usually better to stick to <%= %> expressions to embed the values affected by the loop. The following code would be a better choice:

```html
<% SCAN %>
   Name: <input id="txtName_<%= Tquery.Pk %>" name="txtName_<%= Tquery.Pk %>"
                      value="<%= TQuery.Name %>" />
   
<%  ENDSCAN  %>
```

This gives you full control over the naming for the control although it's a bit more verbose.

### Commenting Markup blocks with <%-- --%> 
You can also comment any markup code in your pages by wrapping <%-- --%> around the HTML, Control, Expression or Script markup.

Commenting occurs at the beginning of the page parsing and causes the markup/controls/code in question not to be parsed - it's effectively stripped from the output altogether. Any controls wrapped in the comment block are not executed and no output is rendered for the commented block.