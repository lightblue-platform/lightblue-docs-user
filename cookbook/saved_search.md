# Saved Searches

Saved searches are predefined search requestes that are stored in the
database. They can be run by providing necessary parameters.

Saved searches are stored as a regular database entity. To setup saved search support:

 * Edit the [saved search metadata](https://github.com/lightblue-platform/lightblue-core/blob/master/misc/src/main/metadata/lb-saved-search.json):
    * Change datastore information to specify where the saved searches are stored
    * Default saved search schema allows access for anyone, change that as necessary
    * Change the version of the saved search metadata
    * Set that version as the default version
    * Create the metadata in the database

 * Edit lightblue CRUD server configuration:

```
{
   "savedSearch" : {
      "entity":"savedSearch",
      "entityVersion":"1.0.0",
      "cacheConfig":"maximumSize=1024,softValues"
   },
   "controllers": [
     ...
   ]
}
```

where:
   * entity: Name of the saved search entity, as defined in the saved search metadata
   * entityVersion: Version of the saved search entity. If omitted, default version is used
   * cacheConfig: [Guava cache specification](http://google.github.io/guava/releases/21.0/api/docs/com/google/common/cache/CacheBuilderSpec.html) for the saved search cache

