All control inherit from wwWebControl and so rely on this base class for most of their internal functionality. Because most controls rely on base behavior of the wwWebControl class it's important to remember that if you are overriding any methods or events of the controls you should always call DoDefault() to make sure the base behavior gets called.

### The Importance of DoDefault()
The base behavior is especially important for container controls that need to fire events into their child controls. Containership events are fired from the parent control into the child control. So for example OnInit fires on the Page object, which in turn calls the OnInit in each of the child controls. If you override the OnInit() metod on the Page and forget to call DoDefault() the child controls won't fire teir OnInit events. This may or may not be a problem depending on whether the child controls have logic in these events. As you might expect this can lead to some really hard to debug bugs in your code.

As a hard and fast rule: Always call DoDefault() when overriding any control methods either on Custom controls or User Controls or Page objects.

There are two exceptions to this rule:
* The Page.OnLoad  event
* The Page.OnPreRender event

Both of these AUTOMATICALLY call their child control events. The reason for this inconsistency is that these two methods are the most commonly implememented in your applications and this way you won't have to remember the DoDefault().