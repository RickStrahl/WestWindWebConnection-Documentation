When creating COM based projects it's extremely important to be careful of how you generate your COM objects and how they get registered on the Web servers that they run on. Basically, COM is based on unique CLASSIDs that determine the entries in the registry for your server. When a project is built into an EXE or DLL file in Visual FoxPro the CLASSID is part of the project - create a new project and the CLASSIDS differ. This can cause problems if you register the server, then copy an update with a different classid to the Web server resulting in a server that doesn't respond. Even if you fully re-register your servers with /regserver and DCOMCNFG you may still have problems with multiple orphaned entries in the registry and DCOMCNFG.

This is a COM issue and occurs with any COM server built in this fashion in any development environment. It's crucial you build your servers consistently before deploying. The appropriate steps are:

<ul>
* Always make sure you build your servers from the same project before deployment. We use a staging server that also serves as the standard project building machine that produces the final EXE files.

* If you do build from a different project make sure you unregister the old server, then register the new one. You'll have to reset all the DCOMCNFG settings
</ul>

### Cleaning up the registry and DCOMCNFG
If you do end up with multiple servers having been registered you can end up with multiple DCOMCNFG entries for the same server. The real server will always show as a Local Server and will have 4 or 5 (in Win2000) tabs of information available including the first page that contains the path to the server. All invalid references will show as Remote Servers.

You have to clean up these entries in the registry and do so manually. Start by unregistering the server with <yourexe> /unregserver.

Then go into RegEdit and find all reference to your ProgId and then clean up each of the subtrees that they are associated with by deleting them out of the registry. There will likely be a lot of references to be deleted (at least 4 per entry in DCOMCNFG). When you're done, go into DCOMCNFG and make sure the server listing is gone.

Once that's done, recompile the project or re-register the server and make sure you always compile the same project for the final file that goes on the server.

It's a hassle, but unfortunately that's how COM works.