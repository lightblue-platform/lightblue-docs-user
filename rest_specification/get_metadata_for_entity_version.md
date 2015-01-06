# GET: Metadata for Entity Version
Get metadata details for the specified version of an entity.

### Request
Two path parameters on the request.
```
GET /metadata/{entityName}/{version}
```

### Response: Success
If the requested version of the entity exists a JSON document matching the [metadata JSON-schema](https://raw.github.com/lightblue-platform/lightblue-core/master/metadata/src/main/resources/json-schema/metadata/metadata.json) is returned.

If the requested version of the entity does not exist no result is returned (empty document).

### Response: Errors
Additional error codes:
* metadata:NoEntityName - no entity name specified
* metadata:NoEntityVersion - no entity version specified
