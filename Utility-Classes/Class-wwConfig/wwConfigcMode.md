This property allows specifying the operational mode of the Config object. 

Supported modes are:
<ul>
* INI (default)
* XML
* REGISTRY
</ul>
If you set the mode you can always call Load() or Save() methods instead of having to call the individual methods (LoadIni, LoadRegistry etc.).

cFileName and/or cRegNode/cRegPath must still be set prior to making the calls to Load/Save.