# Audit Subset of Changes
Let's take the "Audit All Changes" example and reduce what gets audited.  Let's say that our company tracks an ISO2 code with countries but doesn't use it and doesn't care when it changes.  To restrict what gets audited a `projection` would be added to the audit hook configuration in `entityInfo`.

Projection details are documented in the [Query Language Specification](/language_specification/query.html#projection).  This example includes all fields and then excludes the iso2Code field.

```javascript
{
    "entityInfo": {
        "name": "country",
        "hooks": [
            {
                "name": "auditHook",
                "projection": [
                    {
                        "field": "*",
                        "include": true,
                        "recursive": true
                    },
                    {
                        "field": "lastUpdatedBy",
                        "include": false
                    }
                ],
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
