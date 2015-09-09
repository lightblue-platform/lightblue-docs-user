# Value Generators

If you want Lightblue to automatically populate a field which is not set or null (e.g. generate id or current date), then you need to define a value generator.

Note: Value Generators are available since Lightblue version 1.7.0.

Generator types available:
## UUID

TBD

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
```
            "_id": {
                "type": "integer",
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

TBD
