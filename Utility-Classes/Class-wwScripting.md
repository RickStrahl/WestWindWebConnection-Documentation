The `wwScripting` class provides a simple mechanism for executing ASP style `<% %>` script pages containing Visual FoxPro code. The class provides a mechanism for dynamically executing script templates including full semantics for compilation and runtime version checking to provide optimal execution performance as pure FoxPro code.

The class also has support for:

* Layout Pages (master page that content pages can load into)
* Partial Pages (child pages that get loaded into a parent page)

Code execution runs against ASP style script which can evaluate script expressions (`< %=`  and  `% >`) and full code blocks (`< %` and `% >`). The script parser converts templates into pure FoxPro `PRG` file source code that is executed on disk using the `RenderAspScript()` method or from memory using the `ExecScript()` method (more limited).

The `wwScripting` class is used as the script parser for [Response.ExpandScript](vfps://Topic/_1VO07HGB0) and generic script execution in the Web Connection Framework.