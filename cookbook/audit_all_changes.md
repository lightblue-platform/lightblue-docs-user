# Audit All Changes
To audit when data is changed (Create, Update, Delete) we need to add the hook configurations for each.  Let's say for this example we want to audit changes to any of the fields.  To do this we need to configure the `insert`, `update`, and `delete` operations in the `entityInfo`.


A few things to note about this configuration:

* the `hooks` array each object has several values.  Most are documented in the [entityInfo JSON Schema](https://github.com/lightblue-platform/lightblue-core/blob/master/metadata/src/main/resources/json-schema/metadata/entityInfo.json).  One thing not documented is the `configuration` field, which is specific to each hook implementation.  For the audit hook, configuration supports the following fields:
    * entityName - the name of the entity in which to store audit information
    * version - the version of the entity used for storing audit information
* there is no `projection` field specified in this example.  This is because this configuration is auditing all data changes to all fields.  Other examples will cover projections.
* the `find` action was not included.  Including `find` would have a side effect of auditing any read operations against data.  This is included in case there is a case that it's useful, but in practice it is expected this won't be used much.

```
{
    "entityInfo": {
        "name": "country",
        "hooks": [
            {
                "name": "auditHook",
                "configuration": {
                    "entityName": "audit",
                    "version": "1.0.0"
                },
                "actions": [
                    "insert",
                    "update",
                    "delete"
                ]
            }
        ],
        "datastore": {
            "backend": "mongo",
            "datasource": "mongodata",
            "collection": "country"
        }
    },
    "schema": {
        "name": "country",
        "version": {
            "value": "1.0.0",
            "changelog": "Initial version"
        },
        "status": {
            "value": "active"
        },
        "access": {
            "insert": ["anyone"],
            "find": ["anyone"],
            "update": ["anyone"],
            "delete": ["anyone"]
        },
        "fields": {
            "name": {
                "type": "string"
            },
            "iso2Code": {
                "type": "string"
            },
            "iso3Code": {
                "type": "string"
            },
            "lastUpdatedBy": {
                "type": "string"
            }
        }
    }
}
```


