The object on which methods are called. Defaults to the Page.

Note: If you're implementing a custom control or user control and you want to route requests to that control's methods you have to explicitly assign the TargetObject property as early as possible in the control's initialization code:

```foxpro
FUNCTION OnInit()
this.HeaderProxy.TargetObject = this
DODEFAULT()
RETURN
```