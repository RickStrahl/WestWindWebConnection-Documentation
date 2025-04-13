Use this method to *ping* a Web site to check whether a valid Internet connection can be made. 

You provide a fully qualified URL - preferably a static page that loads fast - to check, and you can specify a timeout value that determines how long this request runs and waits a response. 

If a non-error response is returned the method returns `.T.` If the request times out, or a server error occurs the function returns `.F.`