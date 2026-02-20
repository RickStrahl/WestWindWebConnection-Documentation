Web Connection 7.0 switches from Bootstrap v3 to v4 which is a fairly major change for the Bootstrap framework. Luckily as far as features used by Web Connection's base templates and style sheets, the changes are relatively minor.

Most of the Bootstrap concepts from 3.x stay intact in v4 - there are a lot of new features and components and a few that have changed. However, some of the most common layout concepts and base form control layouts stay pretty much the same between 3 and 4.

### Do you need to upgrade?
If you have an existing application under Bootstrap 3 **you don't have to upgrade it to v4**. Web Connection continues to run with the old templates and things will just continue to work that way. Once a project has been set up there are very few things that Web Connection does that depends on boostrap specific features.

There are a few wwHtmlHelper controls that are affected however you can control the rendering of these controls by using a compiler switch in `wconnect_override.h`:

```foxpro
#UNDEFINE BOOTSTRAP_VERSION
#DEFINE BOOSTRAP_VERSION        3
```

When you set this flag, Web Connection renders several controls - `HtmlDateTextBox()`, `HtmlErrorDisplay()` - based on that setting.

### Things that will break
There's a comprehensive list of all the changes in Bootstrap 4 since 3.x here:

* [Migrating to v4](https://getbootstrap.com/docs/4.0/migration/)
* [GizModo Overview of Migration to v4](https://designmodo.com/migrate-bootstrap-4/)

Of those things most things that you are likely to run into are:

* The Panel, Well, Tooltips and a few others have been converted to Card
* Most foreground and background color styling is now done with `bg-style` and `text-style` classes which simplifies things greatly
* Input Groups have changed to different but more logical if more verbose syntax
* There are some sizing changes in forms


I've changed over two medium sized sites to Bootstrap 4 and it took me two to three hours on each site to fix up all the pages. Most of the changes are search and replace plus a few manual realignments. The Bootstrap control docs are a great help for this and I ended up creating a number of Code Snippets for this for [Visual Studio](VFPS://Topic/_5BB19P27H) and [Visual Studio Code](VFPS://Topic/_5C819LHKW) which are included in Web Connection and these might help recreating certain 'controls' more quickly.

Overall I think it's worth upgrading to v4 if you are actively maintaining an application. The new version is easier and more consistent in use of styles, looks much smoother, fixes many weird issues on mobile devices vs v3, and it makes sure you are working with current technology that is still updated and maintained.