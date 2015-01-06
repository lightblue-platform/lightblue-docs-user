# POST: Set Default Version for Entity
Set the default version for the given entity to the specified version.

### Request
Two path params to represent entity name and version.
```
POST /metadata/{entityName}/{version}/default
```

### Response: Success
The [metadata JSON document](https://raw.github.com/lightblue-platform/lightblue-core/master/metadata/src/main/resources/json-schema/metadata/metadata.json) for the given entity name and version.

### Response: Errors
Additional error codes:
* metadata:NoEntityName - no entity name specified
* metadata:NoEntityVersion - no entity version specified
* metadata:MissingEntityInfo - entity info does not exist
* metadata:MissingSchema - schema does not exist for given entity name + version
