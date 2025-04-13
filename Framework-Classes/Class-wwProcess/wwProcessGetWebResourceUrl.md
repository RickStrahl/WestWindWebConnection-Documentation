Returns a URL to retrieve a Web Resource that is embedded in the application or served by the application dynamically. The URL can be used to embed the resource into the page.

WebResources are loaded with wwServer::AddResource and served with wwProcess::WebResource. This method can be used to get a relative URL returned that allows you to invoke a WebResource dynamically for embedding into the page.

This method will be useful for control developers who want to dynamically embed resources into their applications to avoid having to ship external files with custom controls. An example of a control that uses this mechanism is the wwWebAjax control which uses a JavaScript file that is embedded in this way.