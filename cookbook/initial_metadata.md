# Initial Metadata
This document uses a simplified version of a `country` entity.  This example has a few fields that are not indexed to make reading this document easier.

```
{
    "entityInfo": {
        "name": "country",
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
