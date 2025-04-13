Ghostscript is a free Postscript to PDF manipulation tool that is free and open source license (GNU and a custom license). The licensing - as with all open source - is not straightforward and while free for personal use Ghostscript does require licensing for re-distribution and certain commercial use. Please check the licensing on the the GhostScript Web site. 

Web Connection exposes GhostScript via the wwGhostScript class. To use the class you need to install GhostScript and a Postscript compatible printer driver. wwGhostscript prints reports to Postscript, and then converts the postscript output to pdf using the GhostScript PDF conversion API. 

### Install GhostScript
Download and install Ghostscript from <a href="http://www.ghostscript.com" target="top">www.ghostscript.com</a>. Make sure you install the **32 bit version** of Ghostscript so FoxPro can call the GhostScript DLL.

You can also use Chocolatey:

```ps
> choco install ghostscript
```

### Install a PostScript Printer Driver
wwGhostScript works by first printing output to a PostScript file and then converting that output file to a PDF document. To do this you need to install PostScript printer driver on Windows which you can then reference from the wwGhostScript class.

Default Windows PostScript printers available for specific versions of Windows:

* Xerox PS Class Driver (Win 10, Windows Server 2016)
* Xerox Phaser PS Color Class Driver (Win 8)
* Xerox Phaser 6120 PS (Win 7)
* Xerox Phaser 1235 PS (Vista)
* Apple Color LW 12/660 PS (XP and prior)

Once the Printer driver is installed you need to assign the printer driver and optionally configure other settings:

* Set the cPrinterDriver property to this printername
* Optionally set the display resolution
* Include `wwipstuff.dll` as it converts the GhostScript conversion driver code

### Example
```foxpro
oPDF = CREATEOBJECT("wwGhostScript")
oPDF.cPrinterDriver = "Xerox PS Class Driver"   
oPDF.cResolution = "300x300"     && optional
oPDF.PrintReport("customer.frx","d:\temp\customer.pdf")
```

Instead of setting the printer driver explicitly here, you can set the printer driver via the `PostscriptPdfPrinterDriver` setting in the `yourApp.ini` file. In the Ini file:

```ini
[Main]
Postscriptpdfprinterdriver=Xerox PS Class Driver
```

Then in your code:
```foxpro
oPDF = CREATEOBJECT("wwGhostScript")
oPDF.cPrinterDriver = Server.oConfig.cPostscriptPdfPrinterDriver
oPDF.cResolution = "300x300"     && optional
oPDF.PrintReport("customer.frx","d:\temp\customer.pdf")
```

### Installing a specific printer Driver
To install the default Xerox Phaser 1235 PS printer driver easily you can use the following code from the comand window:

```foxpro
DO WCONNECT
InstallPrinterDriver("Xerox PS Class Driver")
```

Or you can use the Console from the command line:

```
Console.exe INSTALLPRINTER "Xerox PS Class Driver"
```