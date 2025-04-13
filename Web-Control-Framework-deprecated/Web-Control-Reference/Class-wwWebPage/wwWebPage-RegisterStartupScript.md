Embeds a script into that page that is added at the bottom of the page and allows executing 'startup' code.

This method is useful for executing clientscript code that needs to run when the page first loads. For example - popping up notifications, re-navigating the page, setting up timers etc.

Note: This code is simply located at the bottom of the page just inside the </form> tag. There's no guarantee that all items on the page like images have loaded but the DOM will be able to see all controls that have been loaded on the page.