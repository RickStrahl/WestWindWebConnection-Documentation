Similar to the Add method this method allows adding an item to the collection. Unlike Add() though this method will not check if a key of the same name exists and simply add it. This means it's possible to insert duplicate keys.

Be careful with this method and only call it if you know for sure there will be no key duplication or you're not accessing the items by key value.