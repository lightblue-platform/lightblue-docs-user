# Metadata

## Entity Metadata
Entity metadata are stored in a collection named "metadata". Format of metadata is:

```javascript
{
    "entityInfo": {
        "_id": String,
        "name": String,
        "hooks": [ hook ],
        "indexes": [ index ],
        "enums": [ enum ],
        "datastore":  datastore_info
    },
    "schema": {
        "_id": String,
        "name": String,
        "version": version,
        "status": Status,
        "access": entity_access,
        "fields": { fields_object },
        "constraints": [ constraint ]
    }
}
```

## Entity Info

* entityInfo: data about an entity that is not versioned
  *  _id: &lt;name&gt;|
  *  name: Name of the entity.  Name is the same in entityInfo and schema sections.
  *  hooks: array of individual hook objects

```javascript
    "hooks": [
        {
            "name": hook implementation name,
            "projection": optional, applied against both request and response data, see CRUD spec,
            "configuration": optional, hook specific configuration details,
            "actions": one or more of "insert", "update", "find", and "delete"
        }
    ]
```
   *  indexes: array of index information.  Each index is an array of fields to index

```javascript
    "indexes": [
        {
            "name": optional name of the index,
            "unique": flag to indicate if index is a unique constraint.  if not specified defaults to 'false',
            "fields": [ { "field":"fieldName", "dir":"$asc" },  ... ] Array with fields (paths) that are part of the index
        }
    ]
```
   *  enums: definition of enumerations (aka value sets) that are referenced from schema

```javascript
    "enums": [
        {
            "name": name of the enumeration,
            "values": array of enum values
        }
    ]
```

   *  Datastore: Datastore object. The actual object implementation depends on the backend. Below is for mongoDB. collection field is required. Other fields are implementation specific.

```javascript
    "datastore" : {
        "backend" : "mongo",
        "datasource" : logical name for the datasource,
        "database" : database name
        "collection" : collection name
    }
```


## Schema

* schema: data about an entity that is versioned
  *  _id: &lt;name&gt;|&lt;version&gt;
  *  name: Name of the entity.  Name is the same in entityInfo and schema sections.
  *  version: The version data for the metadata
     *  value - the version string [required]
     *  extendVersions - array of version values this version extends (implies merges) [optional]
     *  changelog - text describing the version  [required]

```javascript
"version" : {
    "value": String,
    "extendVersions": [ String, ... ],
    "changelog": String
}
```

   * status: Entity metadata status
```javascript
    "status" : {
      "value": one of "active", "deprecated", or "disabled",
      "log": [
        { "date": date,
          "value": String,
          "comment": String }, ... ]
    }
```
   *  fields: object with fields that define the model for the entity.  Structure is based on json schema v4-draft.  See metadata schema.
   *  entity_access: entity access object. Determines the access rights of the entity
```javascript
    "access": {
      "insert": [role1, role2, ...],
      "find": [role1,role2,...],
      "update": [role1,role2,...],
      "delete": [role1,role2,...]
    }
```

If the caller does not have the required role for the operation, a
(not allowed) error entity will be returned constraints: array of
entity constraint objects. Custom constraints can be defined, and
parsers/handlers can be registered for processing. Each constraint is
an object containing one field. The field name determines the
constraint type. Depending on the constraint, the constraintInfo can
be a simple value, an array, or an object:
```javascript
       { "constraintName" : constraintInfo  }
```


### fields_object

* "name": name of the field, this is the property name in the json document
* type: one of:
   *  basic types: boolean, integer (64-bit int), string, double, biginteger, bigdecimal, date, binary, uid
       * If a field is defined as biginteger, or bigdecimal in metadata, store the value as string, and don't allow &lt; &gt; operators during search.operators are not supported for this field during search.
       * binary - an open-ended data type for storing binary data, it is recommended that each use of this field clearly indicates what encoding is used.  For example, in java using DatatypeConverter.printBase64Binary(byte[]) the encoding spec is http://www.w3.org/TR/xmlschema-2/#base64Binary.  Note there are limits on document sizes, see [MongoDB Limits and Thresholds](http://docs.mongodb.org/manual/reference/limits/#BSON-Document-Size) documentation.
       * uid - unique identifier field that can be used anywhere within a document, specifically created to support identity of array elements
   * date: Re: How will we represent date in JSON?
   * a container type: object, array
* fields: If type=object, an array of field objects
* items: If type=array, an object of the form:
```javascript
    "items" : {
      "type": type
      "fields" : { ... } (if type=object)
    }
```
* reference: If type=object or array. If reference is present, fields and elements cannot be given. Determines a query, projection, and sort order for another entity to be inserted into the document
```javascript
    "reference" : {
      "entity": entityName,
      "versionValue": versionValue,
      "projection": projection_expression,
      "query": query_expression,
      "sort": sort
    }
```
* access: field access object. Determines the access rights of the field
```javascript
    "access" : {
       "find" : [ role1, role2, ... ],
       "insert": [ role1, role2, ...],
       "update": [ role1, role2, ... ]
    }
```

  *  find: If the caller does not have the required role, the field is removed from the return value
  *  update: If the caller does not have the required role, the field will not be updated. Attempt to update inaccessible fields will result in failed update
  *  insert: If the caller does not have the required role, attempt to set field value during insertion will result in failed insert.
*  constraints: array of field constraint objects
   *  minLength, maxLength for strings (minLength=1 is to be used to mean nonempty string)
```javascript
    { "minLength" : value }
    { "maxLength" : value }
```
   *  minItems and maxItems limit number of items in an array.  Default is 0 to unlimited.
```javascript
    { "minItems": value }
    { "maxItems": value }
```
   *  required: boolean indicating if the field is required, default is false.
```javascript
    { "required": true|false }
```
   * minimum/maximum (for number types)
```javascript
    { "minimum": value }
    { "maximum": value }
```
   *  enum reference where the enum name comes from entityInfo
```javascript
    { "enum": enum name }
```
   * references: denotes fields in other entities this field depends on. The constraint requires that if this field value is non-null, there must be an instance of entity with { entityField: thisField }
```javascript
    {
        "references": {
           "entityName": entityName,
           "versionValue": version.value
           "entityField": fieldName
        }
    }
```


*  hooks: optional array of enums that define what hooks are triggered when CRUD operations are performed on this field. Hooks are invoked in the given sequence.
   *  Values can be "audit" or "publish" at this time.  See How will events for create, update, and delete be sent to the ESB?
```javascript
    {
        "hooks": {
            "insert":[value1...],
            "update": [value1...],
            "delete": [value1...]
        }
    }
```

### Predefined Fields

The following fields are stored with every entity. If the metadata
defines these as fields, they are exposed to the callers. Otherwise,
these fields will not be exposed.


*  &lt;arrayField&gt;#: arrayField is an array field of the entity. The variable &lt;arrayField&gt;# keeps the number of elements in the array
  * Type: int
  * read-only

* object_type: Entity type
  * Type: string
  * read-only
