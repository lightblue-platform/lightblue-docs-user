# RDBMS

This article aims to give an overview about the RDBMS module, describe its propose and give some fine details of usage.

## RDBMS Overview

Lightblue can have its data storaged in different kinds of databases due its flexible plugin-enabled architeture, it just require the implementation of the business logic to handle the integration.

As some (or all) data can be hard to migrate (or even it can't be migrate due some internal constraints) to another database - in the case of Lightblue default is MongoDB - There is already RDBMS module that makes possible that Lightblue's data be storaged in a SQL database.

To use this module, the user must setup the RBMS enviroment (the database itself and the datasource connection in Application Server) and inform through metadata some information to integrate an entity (in the current version) with the related tables (and procedures) that will be used an relational database to persist entity data. You can also specify the business logic -like the condition and iterate over a variable- to archive that (very similar to the query specification).

## How does it works

To configure your entity’s data be persisted on a RDBMS, you need to inform the datastore field that will have the JNDI for the configured JDBC datasource connection pool on the [datasources.json](https://github.com/lightblue-platform/lightblue/blob/SQLFeatureBranch/lightblue-rest/etc/mongo/datasources.json) which will have the JNDI to the connection pool. By default the datasource that will be used is the one defined in the Entity's [EntityInfo](https://github.com/lightblue-platform/lightblue/blob/SQLFeatureBranch/lightblue-core/metadata/src/main/resources/json-schema/metadata/entityInfo.json) (which is defined in the [Metadata](https://github.com/lightblue-platform/lightblue/blob/SQLFeatureBranch/lightblue-core/metadata/src/main/resources/json-schema/metadata/metadata.json))(The datasource can be overloaded for a specific query using the following [field](https://github.com/lightblue-platform/lightblue/blob/SQLFeatureBranch/lightblue-rdbms/metadata/src/main/resources/json-schema/metadata/rdbms/metadata/operation.json#L66)).

Then, you have to create a special field called rdbms in the EntityInfo content following [this schema](https://github.com/lightblue-platform/lightblue/blob/SQLFeatureBranch/lightblue-rdbms/metadata/src/main/resources/json-schema/metadata/rdbms/metadata/rdbms.json) that will map all the lightblue's operations (insert, save, delete, etc) so the entity can be properly handled by the Lightblue’s RDBMS controller (mapped by [lightblue-crud.json](https://github.com/lightblue-platform/lightblue/blob/SQLFeatureBranch/lightblue-rest/etc/mongo/lightblue-crud.json)).

A simple example of a new Entity Metadata that will use RDBMS for data persistence:
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
        "_id": {"type": "string"},
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

There are two core fields that enable RDBMS schema mapping to be dynamic and flexible: one is 'bindings' field; and the other one is 'expressions' field. These fields are the same for any Lightblue's operation (delete, fetch, ...). In elevator pitch:

*
The bindings helps to the user inform the variables he/she wants to put or collect from the SQL statement.

*
The expressions is a flexible structure that can be recursive and create for loops, conditional statements and evaluation of expression to archive that the SQL statements can run properly.


### Binding

The binding will help you to create a variable to be the intermidiate for inbounds ('in')(the values that need to be inserted into the statements) and outbounds ('out')(the values that need to be extract from the queries). So you can use the 'column' field to use that name in as many statements you want. You can also use those variables for the loop iterate. Below a example of this field:

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


### Expressions

This field holds and array which the objects' types may vary. It can be:


*
SQL statements (insert, delete, select, etc) with an optimal datasource to overload the already defined for this entity
*
Conditional statement, describe the logic to be evaluated, what should happen and 'Else ifs'. This fields makes a recursive reference to expressions again
*
$foreach and $for iterate operators.We can use this field to use each value from the given path. This fields makes a recursive reference to expressions again



