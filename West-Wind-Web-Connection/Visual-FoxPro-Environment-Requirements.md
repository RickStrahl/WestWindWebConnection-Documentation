### Visual FoxPro Versions Supported
Web Connection 7.x (and 6.x) supports only **Visual FoxPro 9.0** to run. 

Web Connection 5.x supports FoxPro 8 and Web Connection 4.x supports FoxPro 7 and 6.

### Visual FoxPro Environment Requirements and Settings

#### Assumes SET EXACT OFF
All West Wind Tools run based on the assumption that `SET EXACT OFF` is set in the FoxPro environment. 

Due to various legacy performance concerns which otherwise would be required to check exact values and existing `SET` flags, `SET EXACT` is assumed to be `OFF`. If you happen to run your code with `SET EXACT ON` you may have to explicitly switch your `SET EXACT` settings before calling functions in this library.  
    
We realize this is not optimal and due to some base framework legacy concerns. We've been slowly removing instances where `SET EXACT` has an impact especially in library code. But there are a still a number of places - especially inside the core Web processing engine - where `SET EXACT` is used extensively and we recommend that you run Web Connection applications with `SET EXACT OFF` and set to `ON` on an as needed basis.