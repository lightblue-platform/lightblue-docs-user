# PUT: Update Entity Info
Create an existing entity's info.

### Request
One path param on the request representing the entity name.
Body of request is a JSON document matching the [enttyInfo JSON-schema](https://raw.github.com/lightblue-platform/lightblue-core/master/metadata/src/main/resources/json-schema/metadata/entityInfo.json)
```
PUT /metadata/{entityName}
{entityInfo JSON document}
```

### Response: Success
On success returns the [metadata JSON document](https://raw.github.com/lightblue-platform/lightblue-core/master/metadata/src/main/resources/json-schema/metadata/metadata.json) with the default version for the given entity.  Note that this is *not* the entityInfo JSON document, it is the metadata JSON document (entity info + schema).

### Response: Errors
Additional error codes:
* metadata:NoEntityName - no entity name specified
* rest-metadata:NoNameMatch - entity name on the path does not match entity name in the json document
* metadata:MissingEntityInfo - entity info does not exist and therefore cannot be updated
