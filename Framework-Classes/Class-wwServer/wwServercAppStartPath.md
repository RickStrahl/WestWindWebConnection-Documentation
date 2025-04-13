This is the application's startup path. This should point to the directory where the server was started in and where wcmain.ini (or your implementation thereof) should reside.

This value is set by wwServer::GetAppBasePath which is gleamed from the registry at startup. This value can be overridden in wwServer::SetServerEnvironment although this is not recommended.