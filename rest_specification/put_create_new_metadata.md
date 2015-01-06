# PUT: Create New Metadata
Create a new entity by defining a new metadata.

### Request
Two path params on the request representing the entity name and version.
Body of request is a JSON document matching the [metadata JSON-schema](https://raw.github.com/lightblue-platform/lightblue-core/master/metadata/src/main/resources/json-schema/metadata/metadata.json)
```
PUT /metadata/{entityName}/{version}
{metadata JSON document}
```

### Response: Success
On success returns the stored metadata document for the newly created version of the entity.

### Response: Errors
Additional error codes:
* metadata:NoEntityName - no entity name specified
* metadata:NoEntityVersion - no entity version specified
* rest-metadata:NoNameMatch - entity name on the path does not match entity name in the json document
* rest-metadata:NoVersionMatch - version on the path does not match the version in the json document
* metadata:DuplicateEntityInfo - entity info for this entity already exists
* metadata:DuplicateSchema - schema for this entity with this version already exists
