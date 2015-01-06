# DELETE: Clear Default Version for Entity
Clear the default version for the given entity

### Request
One path param to represent entity name
```
DELETE /metadata/{entityName}/default
```

### Response: Success
The [entityInfo JSON document](https://github.com/lightblue-platform/lightblue/wiki/Language-Spec-Metadata#entity-info) for the given entity name.

### Response: Errors
Additional error codes:
* metadata:NoEntityName - no entity name specified
* metadata:NoEntityVersion - no entity version specified
* metadata:MissingEntityInfo - entity info does not exist
* metadata:MissingSchema - schema does not exist for given entity name + version
