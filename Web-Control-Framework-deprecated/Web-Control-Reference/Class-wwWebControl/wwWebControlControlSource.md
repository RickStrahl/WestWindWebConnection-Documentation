A ControlSource expression that is evaluated during databinding and assigned to the control's ControSourceProperty.

The Expression() must be valid for the context of the control. If you need to reference properties of the Page object you need to explicitly reference it. Some examples of valid values:

<code>"this.Page.nPk"
"this.Page.oEntry.oData.Company"  && business object
"Request.IsPostBack"
"Server.oConfig.oWebLog.cHtmlPagePath"</code>

Note that you can't use PRIVATE and LOCAL variables defined elsewhere in the class since the call stack doesn't work downwards but on a sibling level. This means any values that you want to bind to either need to be PUBLIC (bad idea) or bound to a property on the Page or Control object.