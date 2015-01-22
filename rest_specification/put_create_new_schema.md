# PUT: Create New Schema
Create a new schema, representing a new version of an existing entity.

### Request
Two path params on the request representing the entity name and version.
Body of request is a JSON document matching the [schema JSON-schema](https://raw.github.com/lightblue-platform/lightblue-core/master/metadata/src/main/resources/json-schema/metadata/schema.json)
```
PUT /metadata/{entityName}/schema={version}
{schema JSON document}
```

### Response: Success
On success returns the [metadata JSON document](https://raw.github.com/lightblue-platform/lightblue-core/master/metadata/src/main/resources/json-schema/metadata/metadata.json) for the newly created version of the entity.  Note that this is *not* the schema JSON document, it is the metadata JSON document (entity info + schema).

### Response: Errors
Additional error codes:
* metadata:NoEntityName - no entity name specified
* metadata:NoEntityVersion - no entity version specified
* rest-metadata:NoNameMatch - entity name on the path does not match entity name in the json document
* rest-metadata:NoVersionMatch - version on the path does not match the version in the json document
* metadata:DuplicateSchema - schema for this entity with this version already exists
