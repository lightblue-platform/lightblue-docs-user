# Unit Testing: FAQ

### How do I keep some hooks, but not all?

Override the *getHooksToRemove()* method to return a *Set&#60;String&#62;* containing the hook names you with to keep. Or if you want to keep all of them, just an empty set or *null*.

### How do I stop access from always being set to 'anyone'?

Override the *isGrantAnyoneAccess()* method to return *false*.

**Note:** This is an all or nothing switch.

### How do I use a different datasources.json, lightblue-metadata.json, or lightblue-crud.json?

Override any or all of the following methods as needed to return the *JsonNode* that you want used by the suite.

* *getLightblueCrudJson()*
* *getLightblueMetadataJson()*
* *getDatasourcesJson()*

### How do I set the 'datasource' to always being the same for production metadata that needs to interact with test metadata?

*AbstractCRUDTestController* and *AbstractMongoCRUDTestController* actually do this somewhat differently. 

If sub-classing the first, then override the *getDatasource()* method. 

If the latter, then set the "mongo.datasource" system property to the datastore name you wish to use. This ensures the all places substitution is used gets correctly set.
