These modes determine how the ASP style (WCS scripts) scripting files are compiled and executed. This flag affects how Response.ExpandTemplate() works and how generic WCS scripts fired through the wwScriptMaps Process class operate.

The following settings can be applied to this flag:
<ol>
* **Dynamic Compilation**  
In this mode script pages are compiled as needed if the WCS files and FXP file are out of sync. If you make a change to the WCS file the parser will detect the change and reparse and recompile the page automatically for you. This mode is the recommended mode while testing and debugging your script pages. It can also be used in deployed applications, but if you run multiple instances and make changes to script pages while servers are running there's no guarantee that all instances will see the changes - typically only the instance that catches the change first will see the new behavior. Only Web Connection server restart will ensure all instances are in sync again.
* Recommended for:* Design time operation. Runtime environments where the pages are not changed frequently.

* **Precompiled FXP files**  
This mode runs only precompiled FXP pages. It's the most efficient mode and it doesn't check for files, but simply tries to execute the FXP file. If it fails no attempt is made to create the file.
* Recommended for:* Runtime environments where best performance is required and files can be precompiled and FXP files can be deployed.

* **Interpreted with ExecScript**  
This mode is completely dynamic and reparses pages on every hit. The page is read from disk and reparsed and executed so this mode is the only one that guarantees that script changes are seen by multiple instances running simultaneously. This mechanism uses EXECSCRIPT() and so parses, compiles and executes a PRG file for every single page access which can be considerably slower than the other two mechanisms.
* Recommended for*: environments where it's vital that pages can be changed at runtime without any restarts.
</ol>

This property is set when the server starts up from the startup ini file using the ScriptMode setting in the [Main] section.