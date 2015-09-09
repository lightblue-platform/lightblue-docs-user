# Value Generators

If you want Lightblue to automatically populate fields (e.g. generate id or current date), then you need to define a value generator.

All value generators have one common configuration flag called overwrite. When set to false (default), sets generated value only if the field of the inserted entity is not set or null. When true, populates the field with generated value regardless.

Note: Value Generators are available since Lightblue version 1.7.0.

Generator types available:
## UUID

Generates a value from ```java.util.UUID.randomUUID()```. Replaces uid type.

```json
            "_id": {
                "type": "string",
                "constraints": {
                    "identity": true
                },
                "valueGenerator": {
                    "type": "UUID"
                }
            },
```


## IntSequence

As it's name implies, the IntSequence value generator generates integer numbers in a sequence. It's primary use case is generating numeric identifiers for documents (approach which is often used in RDBMS).

Here is how _ids were defined so far:
```json
            "_id": {
                "type": "uid",
                "constraints": {
                    "identity": true
                }
            },
```
Uid type is about to be deprecated. UUID generator is to be used instead. Since this section is about IntSequence generator only, I will stop here.

Here is how you can define IntSequence _id:
```json
            "_id": {
                "type": "integer",
                "constraints": {
                    "identity": true
                },
                "valueGenerator": {
                    "type": "IntSequence",
                    "configuration": {
                        "initialValue": "10000",
                        "name": "termsIdIntSequence"
                    }
                },
```

**Important**:
* IntSequence generator name needs to be unique and self-explainatory. Recommended naming pattern: ```<entityName><fieldName><valueGeneratorType>```.
* Generator is defined at the field level, in the versioned schema. This means that multiple versions can exist defining different generators (or complete lack of thereof) for the same field.
* In the example above, type of the field was changed from "uid" to "integer". This is a breaking change and may require data conversion. Note that you could change the type from "uid" to "string" instead (not a breaking change and IntSequence generator would still work as expected), but you'd loose type safety this way (not recommended).

## CurrentTime

Uses ```new java.util.Date()``` to poulate the field.

Create:
```json
            "creationDate": {
                "type": "date",
                "valueGenerator": {
                    "type": "CurrentTime"
                }
            }
```

Update:
```json
            "updateTime": {
                "type": "date",
                "valueGenerator": {
                    "type": "CurrentTime",
                    "configuration": {
                        "overwrite": true
                    }
                }
            }
```
Note the overwrite flag.
