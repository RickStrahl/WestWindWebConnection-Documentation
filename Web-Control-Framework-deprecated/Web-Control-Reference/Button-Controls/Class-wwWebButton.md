The wwWebButton class is the typical 'action' item for a WebPage. Clicking a button usually submits the form and causes a Click event to be fired on the form. 

Note that this interface is pretty sparse, but remember you have access to the the Attributes collection. In markup you can simply add any attributes needed and they will get added. 

So:
```html
<ww:wwWebButton ... AccessKey="S"/>
```

sets the AccessKey attribute as you would expect. From code you can do:

```foxpro
this.btnSubmit.Attributes.Add("AccessKey","S")
```