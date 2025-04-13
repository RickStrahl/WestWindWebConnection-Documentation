This property allows you to specify a fixed filename for the cache. including the .DBF extension. 

When set, the class will use a fixed file to store cache settings which allows multiple instances to share a single cache file.

Remember that if you set this property you will use a FoxPro table that must be packed and Reindexed from time to time to avoid size overloading and index corruption. You can use the Reindex method of this class to pack and reindex the file.