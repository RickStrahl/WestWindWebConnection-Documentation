Configuration class that holds the Error Page mappings. Each of these properties can point at an external page that can be used to display a custom error page for specific errors. 

The filenames can either be expressed as relative Web Paths: 

* messages/Maintenance.htm
* ~/messages/Maintenance.htm

or as absolute physical paths: 

* c:\westwind\wconnect\messages\maintenance.htm

Whatever path files are read from requires that the account running the application (typically SYSTEM) has read access.