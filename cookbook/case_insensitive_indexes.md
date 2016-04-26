# Case Insensitive Indexes
Case insensitive (CI) indexes exist in Lightblue to support easier and more efficient regular expression matching on string fields.

## Creating Case Insensitive Indexes
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

## Querying Case Insensitive Indexes

## Caveats

<bvulaj> mpatercz, what do you foresee being there? How to query / how to create?
<mpatercz> bvulaj, yes. And that you may need both (cs and ci)
