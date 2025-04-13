Web Connection works with [Visual FoxPro Advanced](http://www.baiyujia.com/vfpadvanced/f_vfpa_about.asp) (aka VFP 10). It's possible to run Web Connection under this patched **unofficial** version of Visual FoxPro with a few caveats:

* You have to run the 32 bit version
* West Wind does not officially support this environment
* West Wind doesn't explicitly test Web Connection in this environment
* You are responsible for your own compatibility testing

That said, I've set up Visual FoxPro Advanced and was able to run the Web Connection sample application both in file mode and even under COM:

![](https://support.west-wind.com/PostImages/2021/_UWOY1TKpYxGbEd4f.png)

The example above runs the Web Connection .NET Core local Web Server (also tested in IIS) using COM Mode and it works with all the Web Connection Samples, this message board and a few others. In other words, except for a few small cosmetic issues it looks like **it just works**.

> For setting up Visual FoxPro Advanced I'd recommend you look at [Eric Selje's Southwest Fox White Paper](https://saltydogllc.com/wp-content/uploads/SELJE-VFP-Advanced.pdf) which provides much better install instructions than the Web site does. 

### Why No 64 Bit
The main reason for this limitation is that Web Connection uses a 32 bit support assembly in `wwipstuff.dll`. There are a number of commonly used features in this DLL that provide things like .NET hosting, some high performance conversions and some old integrations for things like the old Zip functions. While it might be possible to move some of this over to 64 bit (or dual mode) or move the dependencies over to .NET altogether and remove the DLL some things like the .NET hosting require the C++ code.

### Some issues I ran into
I didn't spend a lot of time with VFPA but even in that short time I ran into a few issues:  

#### config.fpw doesn't load
In my testing I couldn't get `config.fpw` to load either implicitly from the current folder or via the `-c` command switch line switch. Since Web Connection's default project setup relies on `config.fpw` settings for startup paths, you'll have to create a small program to set up your path environment so that dependencies can be found for running and building your application.

I recommend taking the path from `config.fpw` and creating a `SetEnvironment.prg` file:

```foxpro
CLEAR ALL
CLOSE ALL
CLEAR
SET PATH TO \wconnect\classes; \wconnect; .\data; .\helpers   && etc. etc.
SET EXCLUSIVE OFF 
* etc.
```

You can then `DO SetEnvironment` to set up your paths and environment instead of relying on `config.fpw`.

#### UI Inconsistencies
I also ran into a number of problems with application icons in COM mode when running in `INTERACTIVE` user mode on the desktop. The icons sometimes would show, sometimes not, and sometimes show without opacity.

### Should you use VFP Advanced?
Personally I would **not recommend** using VFP Advanced, simply because of the nature of process of how this product patches Visual FoxPro 9 and because it's impossible to track problems. The author does not have an easily accessible support mechanism or a message board where issues are tracked.

My concern is simply that this tool patches Visual FoxPro at the binary level, rather than from changes in Source Code. These fixes are made most likely from decompiled code which which is tricky at best (especially knowing how byzantine even the original MS source code was). I can't imagine that the author goes through extensive regression of the entire product as Visual FoxPro did for official Microsoft releases. Although the changes and fixes are relatively small and focused they do have the potential to affect other parts of the product. Especially the 64 bit version had to touch a lot of the codebase to work.

The work done here is substantial and impressive, but it lacks a process that can be tracked in a meaningful way. I'd be worried that maybe at some point in the future I'd run into some obscure side effect that's rarely hit.

The bottom line is this: Use this with caution and make sure you test this extensively before you commit to using it. And above all make sure you're actually getting a benefit from using it as the fixes are all relative small and very specific.