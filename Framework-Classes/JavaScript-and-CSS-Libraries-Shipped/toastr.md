**toastr** is a small UI library that shows pop up notifications that pop up, display a message in a variety of styles and then goes away. It's small, convenient and is a great way to display transient error and information messages that warn you of things going on in the application.

**<a href="https://github.com/CodeSeven/toastr" target="top">Toastr on GitHub</a>**  

Here's what a Toast looks like:

![](/images/misc/toastr.png)

### Examples
To use toastr you can then simply call the `toastr` object to display messages from JavaScript code:

```javascript
toastr.info("Hello World");
toastr.error("Server callback failed: Unauthorized access to API.");
toastr.warning("Server callback failed: Unauthorized access to API.");
toastr.success("Customer info has been written","Customer Saved",
               {timeOut: 6000});
```

There are a number of configuration options available using the options object that you can find on the GitHub page (or by peering into the source code). Here are a few common things to set:

```javascript
toastr.options.closeButton = true;
toastr.options.positionClass = "toast-bottom-right";
toastr.options.timeOut = 10000;
```

### Usage

### Usage
You need to add a style sheet and script file in order to use Toastr:

In your Html head:
```html
<link href="~/bower/components/toastr/toastr.min.css" rel="stylesheet"/>
```

At the bootom of the document:
```html
<script src="~/bower-components/toastr/toastr.min.js"></script>
```