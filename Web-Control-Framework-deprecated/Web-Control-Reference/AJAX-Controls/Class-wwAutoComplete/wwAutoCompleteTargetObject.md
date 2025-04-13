Target object instance for the callback that you specify in CallbackHandler. Defaults to the page, but can be overridden to point at another object instance.

Use this property if you're building a custom control that embeds the AutoComplete control. In that case you probably want to map the callback to your custom control rather than the page.

Note this value is set in the OnInit() of the control.