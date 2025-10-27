A number of people have asked for offline documentation for the Web Connection documentation and we're making it available as a separate download from here:

* [Web Connection Documentation Offline Viewer](https://webconnection.west-wind.com/docs/WestWindWebConnection-Documentation-Viewer.exe)

This tool is a separate single EXE file application that runs the documentation as a local Documentation Viewer:

![Web Connection Documentation Viewer](../images/WebConnectionDocumentationViewer.png)

### Not updated frequently as Online Documentation
Please note that we don't update the offline viewer as frequently, so it's always going to be slightly out of date. The online docs are updated as soon as changes are made, while the viewer is posted for interim releases.



> ### @icon-warning Windows SmartScreen Security Warning
> When you download this `.exe` file you likely get a Windows Smart Screen security warning because the executable file is downloaded from the Internet.
>
> ![SmartScreen ForSelfContainedViewer](./SmartScreen-ForSelfContainedViewer.png)
>
> To fix use one of these approaches:
>
> * **Run and bypass SmartScreen**  
>    * Click **More Info** link
>    * The click **Run anyway** button
>
> * **Unblock with Powershell (remove Mark of the Web)**  
>   * Open Powershell and `cd` into your download folder  
>   * ```ps
>     Unblock-File -Path '.\WestWindWebConnection-Documentation-Viewer.exe'
>     ```  
>   * Run again
>   * Unblock removes the *Mark of the Web*