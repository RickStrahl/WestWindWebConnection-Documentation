OnLoadViewState is called after Init and prior to reading POST data. This method retrieves any Viewstate data stored for this control and assigns it. The default implementation provides for most scenarios.

ViewState is page based state that is encoded and sent back to the client on each request. It is encoded (base64) and reassigned when the page is POSTed back to the server. You can use the Control's ViewState object to assign viewstate or you can call PreserveProperty() which saves a specific property and automatically restores it once.

Any value stored in ViewState is automatically persisted until you explicitly clear it.