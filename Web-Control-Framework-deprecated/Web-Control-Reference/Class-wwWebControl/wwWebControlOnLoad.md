OnLoad fires once a control has been fully initialized. This means it has initialized and had its control state loaded either from ViewState or POST data.

OnLoad() fires before any events are fired on the control.

OnLoad() is the Event method that does NOT require DODEFAULT() to be called as the page framework automatically calls the child containers for you. This is inconsitent but done primarily to make the OnLoad() code easier to work with and consistent with ASP.NET.