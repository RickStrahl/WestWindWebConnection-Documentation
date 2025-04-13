Determines whether a sumbit or a plain HTML Button is created when the button is rendered. 

When .T. a type="Submit" is generated and the button posts back to the server whenever clicked. When .F. type="Button" is generated and the button doesn't post back to the server automatically.

The default is .T. and that's the most common use, however if you use buttons for firing JavaScript code on the client you'll want to use SubmitBehavior .F. and then hook up the appropriate client behavior to the onclick or onclientclick.

One common use of this feature is to create buttons that can be used for JavaScript operations or for 'manual' postbacks. For example a manual postback might cause a server method to be called as part of the postback:

```html
<ww:wwWebButton runat="server" ID="btnClient" 
   UseSubmitBehavior="false" 
   onclick="__doPostBack('page','PageMethod');"/>
```