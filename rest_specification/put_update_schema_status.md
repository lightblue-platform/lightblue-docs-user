# PUT: Update Schema Status
Update the status of a a version of an entity.  For example, to disable an old version of an entity.

### Request
Three path params to represent entity name, version, and new status. Optional change comment can be provided as a query param.
```
PUT /metadata/{entityName}/{version}/{status}?comment={Change comment}
```

Values for {status} are defined in the [schema JSON-schema](https://raw.github.com/lightblue-platform/lightblue-core/master/metadata/src/main/resources/json-schema/metadata/schema.json), see #definitions/status/properties/value/enum.

### Response: Success
The [metadata JSON document](https://raw.github.com/lightblue-platform/lightblue-core/master/metadata/src/main/resources/json-schema/metadata/metadata.json) for the given entity name and version.

### Response: Errors
Additional error codes:
* metadata:NoEntityName - no entity name specified
* metadata:NoEntityVersion - no entity version specified
* rest-metadata:NoNameMatch - entity name on the path does not match entity name in the json document
* rest-metadata:NoVersionMatch - version on the path does not match the version in the json document
* metadata:MissingSchema - schema does not exist for given entity name + version and therefore cannot be updated
