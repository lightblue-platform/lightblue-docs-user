# Metadata

* Descriptive name is the Header
* First paragraph is a short description of the API
* Request section contains info on what the request looks like
* Response section contains info on what the response looks like, including errors where applicable
* Example section contains a successful example and an unsuccessful example.

## GET: Entity Dependencies Graph
Get a "graph" of dependencies between entities.  Default versions are used unless a specific entity with version is requested.  If no version is specified and the entity has no default version the dependencies are not returned for that entity.

### Request
Get dependencies for:
* entityName - the name of entity
* version - the specific version
```
GET /metadata/{entityName}/{version}/dependencies
```
---

Get dependencies for entity with default versions.
```
GET /metadata/{entityName}/dependencies
```
---

Get dependencies for all entities with default versions.
```
GET /metadata/dependencies
```
### Response: Success
Returns an array of objects that follow this JSON structure.  Note there is no JSON-schema for this at this time, subject to change.
```
[
    {
        name: <the entity>,
        dependencies: [array of 'this' structure]
    }
]
```

#### Example: cyclic
```
[
    {
        name: "foo",
        dependencies: [
            {
                name: "bar",
                dependencies: [
                    {
                        name: "baz"
                    },
                    {
                        name: "foo"
                    }
                ]
            }
        ]
    }
]
```

### Response: Errors
Additional error codes:
* metadata:MissingEntityInfo - if entity name was specified, entity info does not exist
* metadata:MissingSchema - if version was specified, schema does not exist for given entity name + version


## GET: Entity Roles
Get list of all roles and the entities they allow access to.  See Request for details

### Request
Get all roles for given entity name and specific version.
```
GET /metadata/{entityName}/{version}/roles
```
---

Get all roles for given entity name and default version.  If entity doesn't have a default vesrion an error is returned.
```
GET /metadata/{entityName}/roles
```
---

Get all roles for all entities with default version.  If entity doesn't have a default version an error is returned for that entity.
```
GET /metadata/roles
```

### Response: Success
Returns an array of objects that follow this JSON structure.  Note there is no JSON-schema for this at this time, subject to change.
```
    [
        {
            role: <the role>,
            insert: [array of paths],
            find: [array of paths],
            update: [array of paths],
            delete: [array of paths]
        }
    ]
```
array of paths - each "path" starts with at least an entity name.  If there is no sub-path it is access to the full entity.  If it contains a sub-path it is access to a specific field on that entity.

#### Example: user.find
```
    [
        {
            role: "user.find",
            find: ["user"]
        }
    ]
```
#### Example: user.credentials.write
```
    [
        {
            role: "user.credentials.write",
            insert: ["user.credentials"],
            find: ["user.credentials"],
            update: ["user.credentials"],
            delete: ["user.credentials"]
        }
    ]
```
#### Example: user.credentials.read
```
    [
        {
            role: "user.credentials.read",
            find: ["user.credentials.read"]
        }
    ]
```
### Response: Errors
Additional error codes:
* metadata:MissingEntityInfo - if entityName is supplied, entity info does not exist
* metadata:MissingSchema - if version is supplied, schema does not exist for given entity name + version
* ERR_NO_METADATA - the entity doesn't have default version

## GET: Entity Names
Get names of all defined entities.  There is no paging for this request, all entity names are returned in one response.

### Request
Get all entity names with schemas in the given status list.  List is a comma separate lis of: active, deprecated, and/or disabled.
```
GET /metadata/s={comma separated status list (active, deprecated, disabled)}
```
---

Get all entity names.
```
GET /metadata
```

### Response: Success
JSON document that is an array of strings.  Each element in the array is an entity name.

## GET: Versions for Entity
Get a list of all available versions of a given entity.

### Request
One parameter as a path param of the request.
```
GET /metadata/{entityName}
```

### Response: Success
JSON document that is an array of version information objects.
```
[
  {"version": <versionValue>,
   "changelog":<text>,
    "extendsVersions":[ <version>,...],
    "status":"active"|"disabled"|"deprecated",
    "defaultVersion":true|false
  },
    ...
]
```

### Response: Errors
Additional error codes:
* metadata:NoEntityName - no entity name specified

## GET: Metadata for Entity Version
Get metadata details for the specified version of an entity.

### Request
Two path parameters on the request.
```
GET /metadata/{entityName}/{version}
```

### Response: Success
If the requested version of the entity exists a JSON document matching the [metadata JSON-schema](https://raw.github.com/lightblue-platform/lightblue/master/lightblue-core/metadata/src/main/resources/json-schema/metadata/metadata.json) is returned.

If the requested version of the entity does not exist no result is returned (empty document).

### Response: Errors
Additional error codes:
* metadata:NoEntityName - no entity name specified
* metadata:NoEntityVersion - no entity version specified

## PUT: Create New Metadata
Create a new entity by defining a new metadata.

### Request
Two path params on the request representing the entity name and version.
Body of request is a JSON document matching the [metadata JSON-schema](https://raw.github.com/lightblue-platform/lightblue/master/lightblue-core/metadata/src/main/resources/json-schema/metadata/metadata.json)
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

## PUT: Create New Schema
Create a new schema, representing a new version of an existing entity.

### Request
Two path params on the request representing the entity name and version.
Body of request is a JSON document matching the [schema JSON-schema](https://raw.github.com/lightblue-platform/lightblue/master/lightblue-core/metadata/src/main/resources/json-schema/metadata/schema.json)
```
PUT /metadata/{entityName}/schema={version}
{schema JSON document}
```

### Response: Success
On success returns the [metadata JSON document](https://raw.github.com/lightblue-platform/lightblue/master/lightblue-core/metadata/src/main/resources/json-schema/metadata/metadata.json) for the newly created version of the entity.  Note that this is *not* the schema JSON document, it is the metadata JSON document (entity info + schema).

### Response: Errors
Additional error codes:
* metadata:NoEntityName - no entity name specified
* metadata:NoEntityVersion - no entity version specified
* rest-metadata:NoNameMatch - entity name on the path does not match entity name in the json document
* rest-metadata:NoVersionMatch - version on the path does not match the version in the json document
* metadata:DuplicateSchema - schema for this entity with this version already exists

## PUT: Update Entity Info
Create an existing entity's info.

### Request
One path param on the request representing the entity name.
Body of request is a JSON document matching the [enttyInfo JSON-schema](https://raw.github.com/lightblue-platform/lightblue/master/lightblue-core/metadata/src/main/resources/json-schema/metadata/entityInfo.json)
```
PUT /metadata/{entityName}
{entityInfo JSON document}
```

### Response: Success
On success returns the [metadata JSON document](https://raw.github.com/lightblue-platform/lightblue/master/lightblue-core/metadata/src/main/resources/json-schema/metadata/metadata.json) with the default version for the given entity.  Note that this is *not* the entityInfo JSON document, it is the metadata JSON document (entity info + schema).

### Response: Errors
Additional error codes:
* metadata:NoEntityName - no entity name specified
* rest-metadata:NoNameMatch - entity name on the path does not match entity name in the json document
* metadata:MissingEntityInfo - entity info does not exist and therefore cannot be updated

## PUT: Update Schema Status
Update the status of a a version of an entity.  For example, to disable an old version of an entity.

### Request
Three path params to represent entity name, version, and new status. Optional change comment can be provided as a query param.
```
PUT /metadata/{entityName}/{version}/{status}?comment={Change comment}
```

Values for {status} are defined in the [schema JSON-schema](https://raw.github.com/lightblue-platform/lightblue/master/lightblue-core/metadata/src/main/resources/json-schema/metadata/schema.json), see #definitions/status/properties/value/enum.

### Response: Success
The [metadata JSON document](https://raw.github.com/lightblue-platform/lightblue/master/lightblue-core/metadata/src/main/resources/json-schema/metadata/metadata.json) for the given entity name and version.

### Response: Errors
Additional error codes:
* metadata:NoEntityName - no entity name specified
* metadata:NoEntityVersion - no entity version specified
* rest-metadata:NoNameMatch - entity name on the path does not match entity name in the json document
* rest-metadata:NoVersionMatch - version on the path does not match the version in the json document
* metadata:MissingSchema - schema does not exist for given entity name + version and therefore cannot be updated

## DELETE: Clear Default Version for Entity
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

## POST: Set Default Version for Entity
Set the default version for the given entity to the specified version.

### Request
Two path params to represent entity name and version.
```
POST /metadata/{entityName}/{version}/default
```

### Response: Success
The [metadata JSON document](https://raw.github.com/lightblue-platform/lightblue/master/lightblue-core/metadata/src/main/resources/json-schema/metadata/metadata.json) for the given entity name and version.

### Response: Errors
Additional error codes:
* metadata:NoEntityName - no entity name specified
* metadata:MissingEntityInfo - entity info does not exist
* metadata:MissingSchema - schema does not exist for given entity name + version

## POST: Set Default Version for Entity
Set the default version for the given entity to the specified version.

### Request
Two path params to represent entity name and version.
```
POST /metadata/{entityName}/{version}/default
```

### Response: Success
The [metadata JSON document](https://raw.github.com/lightblue-platform/lightblue/master/lightblue-core/metadata/src/main/resources/json-schema/metadata/metadata.json) for the given entity name and version.

### Response: Errors
Additional error codes:
* metadata:NoEntityName - no entity name specified
* metadata:NoEntityVersion - no entity version specified
* metadata:MissingEntityInfo - entity info does not exist
* metadata:MissingSchema - schema does not exist for given entity name + version

## DELETE: Remove entity
Remove an entity if all its versions are disabled.

### Request
Delete given entity.
```
DELETE /metadata/{entityName}
```

### Response: Success
Empty string

### Response: Errors
Additional error codes:
* metadata:NoEntityName - no entity name specified
* metadata:CannotDeleteEntity - Entity cannot be deleted
