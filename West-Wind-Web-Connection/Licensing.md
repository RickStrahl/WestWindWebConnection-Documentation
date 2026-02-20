Web Connection is licensed per developer and/or per physical or virtual Windows Server instance. Licenses are assigned for a specific **major version** (8.x, 7.x, 6.x etc.) of Web Connection or one of its runtime licenses.

Web Connection works off **license counts**, each of which can be applied either to a single developer **or** a single server installation.

## License Types
Licenses come in two flavors:

* **Full Developer License**  
At least one full developer license is required in order to add any runtime licenses. The developer license includes 2 license counts: One developer and one server installation, so you can build and deploy one Web Connection application.

* **Runtime License**  
A runtime license can be purchased once at least a single developer license exists. Runtime licenses come in various license counts from 5 to unlimited.

Runtime Licenses can be used interchangeably for:

- **Physical/Virtual Server Runtime Licenses**  
A runtime license count can be applied to a single physical or virtual Windows instance. (essentially if you require a Windows license you need a Web Connection Runtime license for that Windows instance)

- **Per Developer Licenses**  
A runtime license count can be applied to a single developer working on a Web Connection application.

### Full Developer License

A **Full Developer License** is required for working with Web Connection during development. This license includes source code access for a single developer to build any number of applications. 

Additional developers in an organization can be added with Runtime licenses that are used for developers or additional developer licenses.

**At least one Developer License is required** per organization, before you can apply any Runtime or additional Developer Licenses as that provides the source code.

The Full Developer License includes:

- **One development seat** (per developer)
- **One server runtime license**
- **Source code for FoxPro components**

### Runtime License
Runtime licenses can be purchased in addition to a developer license to add additional installations or developers seats. Runtime licenses are available with several different license counts.

A single license count of a **Runtime License** can be used in two ways:

- To license **one Windows Server or Virtual Machine (VM) instance**
- To license **one additional developer**

### Purchasing Licenses and Upgrades
Here are the links to purchase licenses for Web Connection:

- [Buy Developer License](https://store.west-wind.com/product/wconnect80)
- [Buy Developer Upgrade](https://store.west-wind.com/product/wconnect80_UP)
- [Buy Runtime License](https://webconnection.west-wind.com/purchase.aspx)
- [Runtime License Upgrade Policy](#license-upgrades)


### Full Developer Software and License
A full Web Connection Developer License ships with a **one Developer License and one Server Runtime License**, so a single developer can both build and deploy one or more applications. Development can be done on any machine or machines as long as a single developer is using the license, and a deployed application can be installed **on a single physical or virtual Windows server**. 

The full developer license also comes with the source code for the FoxPro bits of the framework required to **build your application into a self-contained FoxPro executable** you can deploy to your server. The source code also facilitates debugging and extending the existing functionality during development. 

Because Source Code is included in the Developer License only **at least one developer license is required** before additional runtime licenses can be applied.

> Effectively the single developer/server license counts as 2 licenses that are specifically tied to 1 developer and 1 server installation.

### Developer Runtime Licensing
If you have multiple developers on your team, **each of the developers requires a separate Web Connection license**. You can either use additional full developer licenses (which include one dev and one server license each), or you can use one runtime license and apply it to a developer instead of a machine (see below). If you have multiple developers on your team each developer on the team needs either a full developer license or a license count of one of the runtime license packs.

In general, any more than 2 developers or 2 deployed installations warrants purchase of a Runtime License for savings. 2 developers are better served with 2 developer licenses. 

### Server Runtime Licensing: Per Windows Instance
Web Connection Server Runtime Licenses are directly associated with a **Windows Instance**. 

> For every physical or virtual Windows instance you deploy Web Connection on, you also need a Web Connection Runtime license.**

A Windows instance means either:

* **A Bare Metal Server**     
Running Windows Server (or Client on internal networks) on hardware directly

* **A Virtual Machine**   
Running a virtual instance of Windows on Hyper-V, Virtual Box, Docker or a VPS Hosting service like Azure, AWS, Vultr, Hetzner, Digital Ocean etc. 

> Regardless of physical or virtual instance, each Windows installation requires a separate Web Connection runtime license. 
>

#### Multiple Sites per Windows Instance are allowed
On that physical or virtual instance, you can however run as many Web sites or Domains as you wish. As long as you stay on a single Windows instance, no additional licenses are needed. This means you can host many Web sites running IIS on a single server. Only additional **physical or virtual** machines, whether for production, load balancing, staging or testing require an additional license each.

#### Runtime Distribution
A run-time license allows distribution of a compiled Web Connection application and distribution of any of the binary files included with Web Connection, but server licensing applies to the distributed application. Either you or your client has to have a license for each Windows Instance the distributed application runs on.

Runtime licenses allow for compiled code only and you can issue those licenses to customers from your license pool. 

#### Source Code Distribution requires a Developer License
No portions of the Web Connection framework may be re-distributed as source code without a **full** developer license. If your application distribution for clients requires that the Web Connection source code any part of it is distributed, your client will need a full developer license.

Runtime applications can make use of any of the framework classes supplied by the Web Connection framework including the HTML rendering classes and support tools as long as distributed in compiled form (APP/EXE). 

> ##### @icon-warning Source Code Distribution: Developer License Required
> If you distribute your Web Connection application with source code that includes the Web Connection libraries, each distribution point or end user then requires a full development license. To avoid this requirement, you can ship your application as compiled EXE/APP files.


### License Scenarios
To make it easier to understand licensing here are a few different scenarios for runtime licensing. 

As a broad rule, if you need more than 2 developers or two deployed sites, **runtime licenses offer a large discount over individual Web Connection developer licenses**. Just keep in mind that **a single Web Connection developer license is always required** for any organization in order to purchase additional Runtime Licenses so that you can do development. The runtime licenses do not ship any code - only the developer license does.

#### Single Developer
A single developer can purchase a Web Connection developer license and use that license to develop the application on the local machine, and deploy Web applications to a single live Windows server instance. This means if you are a consultant and you need to build an application for a single customer, the single developer license is all you need.

* [Single Developer License](https://store.west-wind.com/product/wconnect80)

#### Single Developer Multiple Servers
If you're a single developer with multiple deployed physical servers you need to have a single Web Connection developer license, plus additional runtime server licenses for each additional site deployed beyond the first one.

If you have 2 sites deployed your best bet is to use two full developer licenses:

* <a href="https://store.west-wind.com/product/wconnect80" target="top">2 Single Developer Licenses</a>

For more than 2 sites it's cheaper to use the 2 or 5 license pack on top of the full developer runtime:

* <a href="https://store.west-wind.com/product/wconnect80" target="top">Single Developer License</a>
* <a href="https://store.west-wind.com/product/wcrun02" target="top">2 Server Runtime License</a>
* <a href="https://store.west-wind.com/product/wcrun05" target="top">5 Server Runtime License</a>

More Servers:

* <a href="https://store.west-wind.com/product/wconnect80" target="top">Single Developer License</a>
* <a href="https://store.west-wind.com/ItemList_Abstract.aspx?Search=Web+Connection+Runtime" target="top">Runtime Server Pricing</a>

#### Multiple Developers and/or Multiple Sites
If you have multiple developers and multiple sites you'll almost certainly will want to take advantage of the runtime license discounts. A single developer license can be purchased and each developer can use a runtime license count as a developer license.

> **Note:** A single Developer License is always required

Multiple Developers and Server:

* <a href="https://store.west-wind.com/product/wconnect80" target="top">Single Developer Licenses</a>
* <a href="https://store.west-wind.com/ItemList_Abstract.aspx?Search=Web+Connection+Runtime" target="top">Runtime Server Pricing</a>

#### Distributing vertical Web Connection Applications
If you need to distribute a Web Connection application as a vertical application, the same per Windows instance licensing applies: Each physical server that uses the application has to have a license accounted for. Your customers either need to acquire a license on their own, or you can manage that license for your customer by purchasing the appropriately sized license to cover XX number of customer Windows instances. 

One of the licenses is also an [unlimited runtime distribution license](https://store.west-wind.com/product/wcrununlim) that allows you to distribute to as many additional clients **without specific limits on license counts**. For a development shop with a few developers or a shop that serves a number of customers this might be the most cost efficient way to build many applications with multiple developers deployed to many servers.

### Licensing Highlights
The following rules apply:

> ##### @icon-info-circle One Full Developer License Required
> Every Web Connection installation requires **at least one full developer license**. Runtime licenses can then purchased for **additional distribution or developer licenses** on top of the developer license.

> #### @icon-info-circle Every Developer needs a License
> Every Developer using Web Connection to build or debug Web Connection applications has to have a license.

> #### @icon-info-circle Every Windows Server/Client Instance needs a Runtime License
> Every Windows Instance (Server or Client, physical or Virtual Machine) that Web Connection is deployed to requires a license.

> #### @icon-info-circle Base Web Connection License includes One Development and Deploy License
> The base single developer Web Connection license includes a developer license and allows deploying to a single Windows instance for a deployed application. The developer license is essentially all a single developer needs to build and get an application online.


## License Upgrades
All licenses are granted for a specific Web Connection major version (6.x, 7.x, 8.x etc) and moving to a new major version requires a paid upgrade. All upgrade pricing is generally in the neighborhood of 50% of full product pricing.

Major version updates are fairly rare at no less than 5 years, and we guarantee a 1 year free upgrade window from purchase date for the Full Developer License, and 2 years for Runtime Licenses.

* **Licenses are Valid for a Major Version**  
All licenses are valid for the major version (ie. `v8` or `v7`), and are valid for any prior version that it was purchased for.

* **Free Minor Version Updates**  
Minor version updates within the same major version (ie. `v8.1` to `v8.2`) are always free. 

* **Major Version Upgrades**   
Major version upgrades (`v7` to `v8`) are available at discounted rates and **any old version** can be upgraded to the latest version. 

* **One Year Free Upgrade Grace Period**  
Major versions can be upgraded for free if you purchased a previous version within 1 year before the upgrade's release.

* **Runtime Upgrades**  
For runtime upgrade purchases, you can use a **promo code** of **RUNTIME_UPGRADE** with the full product skus, which applies a 50% discount. 

> Note that we manually verify existing versions before any upgrade orders are approved. For this reason upgrade orders may take a day to clear.




## Source Code,  Copyright and License 
You may modify the source code and visual appearance of the Visual FoxPro Web Connection framework **for your own applications only**. Regardless of any changes made to the framework itself, it remains copyright of West Wind Technologies.  Modification of any non Visual FoxPro binary files is not allowed.

## Warranty Disclaimer: No Warranty!
IN NO EVENT SHALL THE AUTHOR, OR ANY OTHER PARTY WHO MAY MODIFY AND/OR REDISTRIBUTE THIS PROGRAM AND DOCUMENTATION, BE LIABLE FOR ANY COMMERCIAL, SPECIAL, INCIDENTAL, OR CONSEQUENTIAL DAMAGES ARISING OUT OF THE USE OR INABILITY TO USE THE PROGRAM INCLUDING, BUT NOT LIMITED TO, LOSS OF DATA OR DATA BEING RENDERED INACCURATE OR LOSSES SUSTAINED BY YOU OR LOSSES SUSTAINED BY THIRD PARTIES OR A FAILURE OF THE PROGRAM TO OPERATE WITH ANY OTHER PROGRAMS, EVEN IF YOU OR OTHER PARTIES HAVE BEEN ADVISED OF THE POSSIBILITY OF SUCH DAMAGES.