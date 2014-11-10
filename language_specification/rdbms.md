# RDBMS
This article aims to give an overview about the RDBMS module, describe its propose and give some fine details of usage.

## RDBMS Overview
Lightblue can have its data storaged in different kinds of databases due its flexible plugin-enabled architeture.  It just requires the implementation of the business logic to handle the specific data storage.

To use this module the user must:

1. setup the RDBMS enviroment
2. make a datasource available to lightblue via JNDI
3. create metadata using the RDBMS datastore, referencing the JNDI above

### How it works
To configure your entity’s data be persisted on a relational database, you need to create metadata that references a valid JNDI name for a datasource connection pool in [datasources.json](https://github.com/lightblue-platform/lightblue-rest/blob/master/etc/mongo/datasources.json). By default the datasource used is defined in the [Metadata’s schema](https://github.com/lightblue-platform/lightblue-core/blob/master/metadata/src/main/resources/json-schema/metadata/schema.json)).

In the Schema of your metadata create an rdbms`field following [rdbms.json](https://github.com/lightblue-platform/lightblue-rdbms/blob/master/metadata/src/main/resources/json-schema/metadata/rdbms/model/rdbms.json). This field maps all the lightblue CRUD operations (insert, find, update, save, delete) so the entity can be properly handled by the lightblue RDBMS controller (mapped by [lightblue-crud.json](https://github.com/lightblue-platform/lightblue-rest/blob/master/etc/mongo/lightblue-crud.json)).

A simple example of a new Entity Metadata that will use RDBMS for data persistence.  The JNDI name for the datasource is `rdbmsdata`:
```
{
  "entityInfo" : {
    "name": "user",
    "datastore": {
        "backend":"rdbms",
        "datasource": "rdbmsdata"
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
        "delete":{"bindings":{...}, "expressions":[...]},
        "fetch":{"bindings":{...}, "expressions":[...]},
        "insert":{"bindings":{...}, "expressions":[...]},
        "save":{"bindings":{...}, "expressions":[...]},
        "update":{"bindings":{...}, "expressions": [...]},
        "dialect":"oracle",
        "SQLMapping":{...}
    }
   },
}
```

### RDBMS Schema at a glance

There are the following core fields that enable RDBMS schema mapping to be dynamic and flexible:

* `bindings` - configures the variables to put or collect from the SQL statement.
* `expressions` - flexible structure of crieteria and conditions for evaluation of an expression.

#### bindings

The binding allow you to specify intermediate placeholders for inbound and outbound data.

* `in` - the values that need to be inserted into the statements
* `out` - the values that need to be extracted from the result set

For each the following attributes are set:
* `column` - the column in the request or result to bind to the given field
* `field` - the name to bind the result in the column to for future use

```
"bindings": {
    "in" : [
        {
            "column" : "NameCustomer",
            "field"   : "name"
        },
        {
            "column" : "y",
            "field"   : "x"
        }
    ],
    "out" : [..]
}
```

#### expressions

This field holds an array of:

* `statement` - SQL statements (insert, delete, select, update, call).
* `if` / `then` - conditional statement describing the logic to be evaluated, what should happen, and optional `elseIf` or `else`.
* `foreach` and `for` - iteration operators used to operate on each value in a given result.

```
 "expressions" : [
    {
        "statement": {
            "sql": "SELECT * FROM DUAL",
            "type": "select"
        }
    },
    {
        "if": {
            "or": [
                {
                    "fieldCheckField": {
                        "field": "x",
                        "op": "gt",
                        "rfield": "y"
                    }
                },
                {
                    "fieldCheckValues": {
                        "field": "z",
                        "op": "in",
                        "values": ["a", "1", "3"]
                    }
                }
            ]
        },
        "then": "fail",
        "elseIf": [
            {
                "if": {
                    "fieldCheckField": {
                        "fieldCheckField": "x",
                        "op": "greaterThan",
                        "rfield": "z"
                    },
                    "then": [
                        {
                            "sql": "SELECT 5 FROM DUAL",
                            "type": "select"
                        }
                    ]
                },
                "else": [
                    {
                        "foreach": {
                            "iterateOverField": "z",
                            "expressions": [
                                {
                                    "sql": "SELECT z FROM DUAL",
                                    "type": "select"
                                }
                            ]
                        }
                    },
                    {
                        "for": {
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

## RDBMS: a deep dive
As already discussed some details around the RDBMS module, now we will focus about its schema. An example of the format of [rdbms](https://github.com/lightblue-platform/lightblue/blob/master/lightblue-rdbms/metadata/src/main/resources/json-schema/metadata/rdbms/model/rdbms.json) is:
```
{
    "delete": operation,
    "fetch":  operation,
    "insert": operation,
    "save":   operation,
    "update": operation,
    "dialect":    RDBMS_VENDOR,
    "SQLMapping": SQLMapping
}
```
* `delete`/`fetch`/`insert`/`save`/`update` : A Operation object for each Lightblue operation

The schema have a contraint that at least one of those fields must be declared (empty object will result on a error message in the response). Lightblue will evaluate during the runtime if the operation requested is informed in the Entity's RDBMS object, if it wasn't informed before hand in the specific version and error message will be returned. If you have an entity persisted that will need all the Lightblue operations you should declare all the fields.

### operation
Each RDBMS's [operation](https://github.com/lightblue-platform/lightblue/blob/master/lightblue-rdbms/metadata/src/main/resources/json-schema/metadata/rdbms/model/operation.json) must follow the below structure:
```
{
    "bindings":      bindings,
    "expressions": [expressions]
}
```
* `bindings`: A Bindings object. Not required. This field will be used to input and output dynamic data from/into the statements
* `expressions`: An array of Expression objects. Required to be declared. It will be used to evaluate some logic conditionals, iterate over a field (from binding or from the entity) and also run SQL statements

### Bindings
The [bindings](https://github.com/lightblue-platform/lightblue/blob/master/lightblue-rdbms/metadata/src/main/resources/json-schema/metadata/rdbms/model/operation.json) must follow the below structure:
```
{
    "in" : [InOut],
    "out": [InOut]
}
```
* `in`: An array of InOut objects. This field stands for the information that the user want to be extracted from the Entity so it can be used in the SQL statement
* `out`: An array of InOut objects. This field will extract the information from the one of the SQL statements and put that in the Entity

This part of the schema requires that you at least declare `in` or `out` field, if you declared a bindings object.

### InOut
The [InOut](https://github.com/lightblue-platform/lightblue/blob/master/lightblue-rdbms/metadata/src/main/resources/json-schema/metadata/rdbms/model/operation.json) must follow the below structure:
```
{
    "column": string,
    "field":  string
}
```
* `column`: is a String. It represent a variable in the SQL statement that will be replaced by the field value if used with 'in' field or the other way round, extracting from the result of an SQL statement to a field if it is in 'out' field.
* `field`: is a String. It will get the information from a Entity's field to be used with 'in' field or the other way round, the field can be filled with the value from the result of an SQL statement.

### expressions
Each RDBMS's [expression element of the array](https://github.com/lightblue-platform/lightblue/blob/master/lightblue-rdbms/metadata/src/main/resources/json-schema/metadata/rdbms/model/operation.json) can be one of the following objects:
```
{
    "statement": statement
}
```
```
{
    "if":     if,
    "then":   then,
    "elseIf": [elseIf],
    "else": "else"
}
```
```
{
    "foreach": foreach
}
```
```
{
    "for": for
}
```

* `statement`: This object represent an SQL statement that needs to run in that informed order
* `if` et al: An conditional object that can make the dynamic SQL statement to run (or even to stop everything) depending on the inputted data
* `foreach`: This operator object will iterate over a field or variable
* `for`: This operator object will loop and create a variable that represent the state of the loop

An expression must follow one of those structures above, otherwise it won't be valid.

### statement
The [statement](https://github.com/lightblue-platform/lightblue/blob/master/lightblue-rdbms/metadata/src/main/resources/json-schema/metadata/rdbms/model/operation.json) can defined as the following structure:
```
{
    "sql": string,
    "type": enum: ["select","insert", "update","delete","call"]
}
```
* `sql`: This SQL statement is a required field of String type that you want to be executed. It have have variables to be inputed or extracted if it is necessary.
* `type`: It represents the type of the SQL statement is. It must be one of the enum above and it is a required field.

### if et al
The [if et al](https://github.com/lightblue-platform/lightblue/blob/master/lightblue-rdbms/metadata/src/main/resources/json-schema/metadata/rdbms/model/operation.json) follows thi structure:
```
{
    "if":     conditional,
    "then":   elseOrThen,
    "elseIf": [elseIf],
    "else":   elseOrThen
}
```
* `if`: This field represent an object  that determinate the coditional that will trigger the execution of the `then` field or other field (if informed) of the same object (`elseif`s and `else`). It is a required field
* `then`: Only executed when the evaluation of 'if' returns true. More information on `elseOrThen`
* `elseIf`: an array of elseIf objects which try a different conditional in can the first if field of this object returned false
* `else`: Only executed when the evaluation. More information on `elseOrThen`

Where the `elseOrThen` is defined as following:
```
"oneOf": [
        {
            [expressions]
        },
        {
            "enum": ["fail","continue","break"]
        }
    ]
```
* `elseOrThen` can be defined with one of two distinct objects. It can be an array of `expressions` (also document in this article), or it can be one of the above enums, which:
    *  `fail`: will rollback the transaction
    *  `continue`: will skip the current loop iteration
    *  `break`: will break the loop iteration


### conditional
The [conditional](https://github.com/lightblue-platform/lightblue/blob/master/lightblue-rdbms/metadata/src/main/resources/json-schema/metadata/rdbms/model/conditional.json) can be defined as one of following objects:
```
{
    "or": [conditional]
}
```
```
{
    "any": [conditional]
}
```
```
{
    "and": [conditional]
}
```
```
{
    "all": [conditional]
}
```
```
{
    "not": conditional
}
```
```
{
    "fieldEmpty": fieldEmpty
}
```
```
{
    "fieldCheckField": fieldCheckField
}
```
```
{
    "fieldCheckValue": fieldCheckValue
}
```
```
{
    "fieldCheckValues": fieldCheckValues
}
```
```
{
    "fieldRegex": fieldRegex
}
```

* `or`/`any`: it is an array of at least two conditional objects. If any of the informed conditionals are asseted as true, it will trigger the then field associated with this field
* `and`/`all`: it is an array of at least two conditional objects. If all of the informed conditionals are asseted as true, it will trigger the then field associated with this field
* `not`: it is a conditional object. If the informed conditional is asseted as false, it will trigger the then field associated with this field
* The other fields are objects which will be described in more details in next sections

### fieldEmpty
The [fieldEmpty](https://github.com/lightblue-platform/lightblue/blob/master/lightblue-rdbms/metadata/src/main/resources/json-schema/metadata/rdbms/model/conditional.json) is structed as following:
```
{
    "field": String
}
```
* `field`: A required String that represents a field from the Entity or an inputed variable. If the evaluation of the field is empty, this conditional will return true, otherwise it will return false

### fieldCheckField
The [fieldCheckField](https://github.com/lightblue-platform/lightblue/blob/master/lightblue-rdbms/metadata/src/main/resources/json-schema/metadata/rdbms/model/conditional.json) is structed as following:
```
{
    "field": String,
    "op": "enum": [
        "eq",
        "neq",
        "lt",
        "gt",
        "lte",
        "gte",
        "in",
        "nin"
    ],
    "rfield": String
}
```
* `field`: A required String that represents a field from the Entity or an inputed variable
* `op`: A required field which must be one of the above enums. A better description of this enum, look on [this reference](../language_specification/rdbms.html#op).It will return the evaluation of the field against rfield using the operation op
* `rfield`: A required String that represents a field from the Entity or an inputed variable

### fieldCheckValue
The [fieldCheckValue](https://github.com/lightblue-platform/lightblue/blob/master/lightblue-rdbms/metadata/src/main/resources/json-schema/metadata/rdbms/model/conditional.json) is structed as following:
```
{
    "field": String,
    "op": "enum": [
        "eq",
        "neq",
        "lt",
        "gt",
        "lte",
        "gte",
        "in",
        "nin"
    ],
    "value": String
}
```
* `field`: A required String that represents a field from the Entity or an inputed variable
* `op`: A required field which must be one of the above enums. A better description of this enum, look on [this reference](../language_specification/rdbms.html#op). It will return the evaluation of the field against rfield using the operation op
* `value`: A required String that represents a simple value to be used to comparison

### fieldCheckValues
The [fieldCheckValues](https://github.com/lightblue-platform/lightblue/blob/master/lightblue-rdbms/metadata/src/main/resources/json-schema/metadata/rdbms/model/conditional.json) is structed as following:
```
{
    "field": String,
    "op": "enum": [
        "eq",
        "neq",
        "lt",
        "gt",
        "lte",
        "gte",
        "in",
        "nin"
    ],
    "values": [String]
}
```
* `field`: A required String that represents a field from the Entity or an inputed variable
* `op`: A required field which must be one of the above enums. A better description of this enum, look on [this reference](../language_specification/rdbms.html#op). It will return the evaluation of the field against rfield using the operation op
* `values`: An array of required String that represents a simple value to be used to comparison

### fieldRegex
The [fieldRegex](https://github.com/lightblue-platform/lightblue/blob/master/lightblue-rdbms/metadata/src/main/resources/json-schema/metadata/rdbms/model/conditional.json) is structed as following:
```
{
    "field": String,
    "regex": String,
    "caseInsensitive": boolean,
    "multiline": boolean,
    "extended": boolean,
    "dotall": boolean
}
```
* `field`: A required String that represents a field from the Entity or an inputed variable.
* `regex`: A required String that must follow the regex standard format. It will be used to be evaluated against the target field
* `caseInsensitive`: A non-required bolean field that can enable or disable the case sensitive of the regex execution
* `multiline`: A non-required bolean field that can enable or disable the multilne usage of the regex execution
* `extended`: A non-required bolean field that can enable or disable the case extend function of the regex execution
* `dotall`: A non-required bolean field that can enable or disable the regex to match any character including a newline

### op
The [op](https://github.com/lightblue-platform/lightblue/blob/master/lightblue-rdbms/metadata/src/main/resources/json-schema/metadata/rdbms/model/conditional.json) is structed as following:
```
"op": "enum": [
    "eq",
    "neq",
    "lt",
    "gt",
    "lte",
    "gte",
    "in",
    "nin"
]
```
* `eq`: The equals operator. Example of A equals B, so A eq B
* `neq`: The not equals operator. Example of A not equals B, so A neq B
* `lt`: The less than operator. Example of 1 less than 2, so 1 lt 2
* `gt`: The greater than operator. Example of 5 greater than 3, so 5 gt 3
* `lte`: The less than or equals operator. Example of 1 less than or equals 1, so 1 lte 2
* `gte`: The greater than or equals operator. Example of 5 greater than or equals 5, so 5 gte 5
* `in`: The in operator. Example of [1,2,3] is in [1,2,3,4], so A in B
* `nin`: The not in operator. Example of [1,3] is in [0,2,4], so A nin B

### elseIf
The [elseIf](https://github.com/lightblue-platform/lightblue/blob/master/lightblue-rdbms/metadata/src/main/resources/json-schema/metadata/rdbms/model/operation.json) can be defined as following:
```
{
    "if": if,
    "then": then
}
```
* `if`: Required If object. Find more information about If Object in this article
* `then`: Required Then object. Find more information about Then Object in this article


### foreach
The [foreach](https://github.com/lightblue-platform/lightblue/blob/master/lightblue-rdbms/metadata/src/main/resources/json-schema/metadata/rdbms/model/operation.json) is structed as following:
```
{
    "iterateOverField": String,
    "expressions": expressions
}
```
* `iterateOverField`: Required String. It will be evaluated in runtime to get a variable or a Entity's field array of values to iterate over it calling the informed expressions
* `expressions`: Required expressions object. These expressions will be evaluated for each value from iterateOverField field

### for
The [for](https://github.com/lightblue-platform/lightblue/blob/master/lightblue-rdbms/metadata/src/main/resources/json-schema/metadata/rdbms/model/operation.json) can be defined as following:
```
{
    "loopTimes": number,
    "loopCounterVariableName": String,
    "expressions": expressions
}
```
* `loopTimes`: Required number. It will be evaluated in runtime to get a variable or a Entity's field to iterate over expressions as many times as it is results
* `loopCounterVariableName`: Required String. It will be the temporary variable that will hold the current state of the loop
* `expressions`: Required expressions object. These expressions will be evaluated for each value from iterateOverField field


### dialect
This field will enable you to  nform to lightblue which RDBMS Vendor is going to be used, so lightblue can generate some specific statements for that vendor. The only value supported right now is "oracle". Example:
```
{
    "dialect": "oracle",
}
```


### SQLMapping
You can use this field to specify the relationship between the entity persisted as a document in lightblue and the tables envolved persisted in RDBMS. It must follow the below structure:
```
{
    "columnToFieldMap":[{
        "table": "t",
        "column": "c",
        "field": "f"
    }],
    "joins": [{
        "tables": [{
          "name": "n",
          "alias": "a"
        }],
        "joinTablesStatement": "x",
        "projectionMappings": [{
          "column": "c",
          "field": "f",
          "sort": "s"
        }]
    }]
}
```
* `columnToFieldMap`: Describe the relationship between eachfield to a column;
* `joins`: Describe the tables, informing the necessary joins, sort alias, etc.
