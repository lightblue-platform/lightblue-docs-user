# Case Insensitive Indexes
Case insensitive (CI) indexes exist in Lightblue to support easier and more efficient regular expression matching on string fields.

## Creating CI Indexes
### Simple Fields
CI indexes are as simple as creating any other index, just with one additional meta field
```javascript
    "indexes": [
            ...
            {
                "fields": [
                    {
                        "dir": "$asc",
                        "field": "firstName",
                        "caseInsensitive": true
                    }
                ],
                "name": "user_firstName"
            },
            ...
          ]
```
### Array & Nested Fields
The same is true for fields embedded in arrays or sub-objects.  Note that arrays *must* have the index placeholder for lightblue to correctly build the index.
```javascript
    "indexes": [
            ...
            // Object field
            {
                "fields": [
                    {
                        "dir": "$asc",
                        "field": "address.zipCode",
                        "caseInsensitive": true
                    }
                ],
                "name": "user_address_zipCode"
            },
            // Array String field
            {
               "fields": [
                    {
                        "dir": "$asc",
                        "field": "nickNames.*",
                        "caseInsensitive": true
                    }
                ],
                "name": "user_nickNames"
            },
            // Object array with string fields
            {
               "fields": [
                    {
                        "dir": "$asc",
                        "field": "friends.*.firstName",
                        "caseInsensitive": true
                    }
                ],
                "name": "user_friends_firstName"
            }
          ]
```

## Querying CI Indexes
CI indexes are currently only hit when utilizing regular expression queries with a CI option.  For example the following query and index would go hand-in-hand.  They would match any variation of *Tom*: `'TOM'`, `'tOm'`, etc.

**Query**
```javascript
    {
        "field": "firstName",
        "regex": "tom.*",
        "options": "i"
    }
```

**Index**
```javascript
    {
        "fields": [
            {
                "dir": "$asc",
                "field": "firstName",
                "caseInsensitive": true
            }
        ],
        "name": "user_firstName"
    }
```

## Caveats
There are a few other caveats to keep in mind when dealing with CI indexes.

1. The index will **not** be hit when using other query types (non regex) or regex queries without the case-insensitive option.
 1. This means that if you plan on dealing with case sensitive queries, or simpler query types, you should create another case sensitive index.  Though you will still need to be mindful of the limitations of that as well.
2. If you create an array-based CI index, you **must** use the index/asterisk (`*`) placeholder.
 1. Lightblue will not know how to create the necessary mechanisms otherwise, and it will not know to use your CI index during regex queries.
3. When creating a new case-insensitive index, the background process required to populate necessary fields is a *long running process*.
 1. Depending on the size of the collection, the documents in it, the fields being updated, and the hardware in the system, the indexing can generally process between 5000 and 10000 records per minute per field. That is +/- 10 hours for a 5 million record collection.
 2. This process does not keep track of state, and therefore the system should be isolated for the duration of the indexing. If the reindixing is disrupted, the process will have to be restarted.
