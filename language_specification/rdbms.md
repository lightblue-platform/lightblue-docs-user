# RDBMS

This article aims to give an overview about the RDBMS module, describe its propose and give some fine details of usage.

## RDBMS Overview

Lightblue can have its data storaged in different kinds of databases due its flexible plugin-enabled architeture.  It just requires the implementation of the business logic to handle the specific data storage.

To use this module the user must:

1. setup the RDBMS enviroment
2. make a datasource available to lightblue via JNDI
3. create metadata using the RDBMS datastore, referencing the JNDI above

## How It Works

To configure your entity’s data be persisted on a relational database, you need to create metadata that references a valid JNDI name for a datasource connection pool in [datasources.json](https://github.com/lightblue-platform/lightblue/blob/master/lightblue-rest/etc/mongo/datasources.json). By default the datasource used is defined in the Metadata's [EntityInfo](https://github.com/lightblue-platform/lightblue/blob/master/lightblue-core/metadata/src/main/resources/json-schema/metadata/entityInfo.json).  The datasource can be overriden for a specific query using the [datasource](https://github.com/lightblue-platform/lightblue/blob/master/lightblue-rdbms/metadata/src/main/resources/json-schema/metadata/rdbms/metadata/operation.json#L66) field in the `$statement` object.

In the entityInfo of your metadata create an `rdbms` field following [rdbms.json](https://github.com/lightblue-platform/lightblue/blob/master/lightblue-rdbms/metadata/src/main/resources/json-schema/metadata/rdbms/metadata/rdbms.json).  This field maps all the lightblue CRUD operations (insert, find, update, save, delete) so the entity can be properly handled by the lightblue RDBMS controller (mapped by [lightblue-crud.json](https://github.com/lightblue-platform/lightblue/blob/master/lightblue-rest/etc/mongo/lightblue-crud.json)).

A simple example of a new Entity Metadata that will use RDBMS for data persistence.  The JNDI name for the datasource is `rdbmsdata`:
```
{
  "entityInfo" : {
    "name": "user",
    "datastore": {
        "backend":"rdbms",
        "datasource": "rdbmsdata",
        "collection": "user"
    }
  },
  "schema" : {
    "name" : "user",
    "version": {
        "value": "1.0.0",
        "changelog": "Test version"
    },
    "status": {
        "value": "active"
    },
    "access" : {
        "insert": ["anyone"],
        "find":["anyone"],
        "update":["anyone"],
        "delete":["anyone"]
    },
    "fields": {
        "id": {"type": "string"},
        "object_type": {"type": "string"},
        "login": {
            "type": "string",
            "constraints": {
                "maxLength": 64,
                "minLength": 1,
                "required": true
            }
        },
        "createdDate": {"type": "date"}
    },
    "rdbms" : {
        "delete": {"bindings":{...}, "expressions":[...]},
        "fetch":{...},
        "insert":{...},
        "save":{...},
        "update":{...}
    }
   },
}
```

## RDBMS Schema

There are two core fields that enable RDBMS schema mapping to be dynamic and flexible:

* `bindings` - configures the variables to put or collect from the SQL statement.
* `expressions` - flexible structure of crieteria and conditions for evaluation of an expression.

### bindings

The binding allow you to specify intermediate placeholders for inbound and outbound data.

* `in` - the values that need to be inserted into the statements
* `out` - the values that need to be extracted from the result set

For each the following attributes are set:
* `column` - the column in the request or result to bind to the given path
* `path` - the name to bind the result in the column to for future use

```
"bindings": {
    "in" : [
        {
            "column" : "NameCustomer",
            "path"   : "name"
        },
        {
            "column" : "y",
            "path"   : "x"
        }
    ],
    "out" : [..]
}
```

### expressions

This field holds an array of:


* `$statement` - SQL statements (insert, delete, select, update, call) with an optional `datasource` to override the default value for the entity.
* `$if` - conditional statement describing the logic to be evaluated, what should happen, and optional `$elseIf` or `$else`.
* `$foreach` and `$for` - iteration operators used to operate on each value in a given result.


```
 "expressions" : [
    {
        "$statement": {
            "datasource": "name",
            "sql": "SELECT * FROM DUAL",
            "type": "select"
        }
    },
    {
        "$if": {
            "$or": [
                {
                    "$path-check-path": {
                        "path1": "x",
                        "conditional": "$greaterThan",
                        "path2": "y"
                    }
                },
                {
                    "$path-check-values": {
                        "path1": "z",
                        "conditional": "$contains",
                        "values2": ["a", "1", "3"]
                    }
                }
            ]
        },
        "$then": "$fail",
        "$elseIf": [
            {
                "$if": {
                    "$path-check-path": {
                        "path1": "x",
                        "conditional": "$greaterThan",
                        "path2": "z"
                    },
                    "$then": [
                        {
                            "sql": "SELECT 5 FROM DUAL",
                            "type": "select"
                        }
                    ]
                },
                "$else": [
                    {
                        "$foreach": {
                            "iterateOverPath": "z",
                            "expressions": [
                                {
                                    "sql": "SELECT z FROM DUAL",
                                    "type": "select"
                                }
                            ]
                        }
                    },
                    {
                        "$for": {
                            "loopTimes": "10",
                            "loopCounterVariableName": "i",
                            "expressions": [
                                {
                                    "sql": "SELECT i FROM DUAL",
                                    "type": "select"
                                }
                            ]
                        }
                    }
                ]
            }
        ]
    }
]
```


