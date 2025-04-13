FontAwesome is a typeface based icon library that provides over <a href="https://fontawesome.com/icons?from=io" target="top">4500 icons</a> (in v5 Pro - 1500 in the Free version) that you can use in your application. Fontawesome updated to version 5.0 which introduced a ton of new icons, plus a new mechanism for specifying icons - although the old style is still supported for backwards compatibility. The new icons in many cases look much cleaner and of course there are many more.

FontAwesome 5 comes in both Free and Pro versions - the Pro version includes many additional icons, mostly alternate icon styles. The free version maintains rougly the same footprint of the old v4 icons while many additional icons are provided for pro. Pro is $60 for a yearly license which seems reasonable if you want to use the extra icons.

### Switching from v4 to v5
Web Connection 7.0 uses FontAwesome v5 Free for its default templates and so it will work out of box with those. 

FontAwesome 5.0 includes a set of mapper styles that make most of the old FontAwesome v4 icon tags work with v5, so for the most part icons are just going to work.

However, v5 uses a different style for icons where:

* `far fa-icon` - Regular style
* `fas fa-icon` - Solid style
* `fal fa-icon` - Light style
* `fab fa-icon` - Brand icon

Note that not all of these styles are available in the free version. Check the <a href="https://fontawesome.com/icons?from=io" target="top">icon listing</a> on the site to see what works with the Free icons.


### Using FontAwesome 5.0 Pro
You can switch to FontAwesome Pro in the following way:

* Download your registered Pro Version as a Zip file
* Open the Zip and copy the Pro version files to
* `web\lib\fontawesome` folder
* Update the the `css` and `webfont` folders

That's it - you're done.