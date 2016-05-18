# Value Generators

If you want Lightblue to automatically populate fields (e.g. generate id or current date), then you need to define a value generator. Behavior:
* if a field is not in the document and is not required, it won't be automatically populated,
* if a field is required and it is not in the document, it'll be inserted and automatically populated,
* if a field is set to null, it will be automatically populated (even if not required),
* if overwrite configuration flag is set to true on the value generator, field will be populated even if it is initialized (already has a value). It will not be inserted if the field is not required and if the field does not exist in the document.

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
                "constraints": {
                    "required": true
                },
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
                    "overwrite": true
                }
            }
```
Note the overwrite flag.
