# Search Criteria
```
query_expression := logical_expression | comparison_expression
logical_expression := unary_logical_expression | nary_logical_expression
unary_logical_expression := { unary_logical_operator : query_expression }
nary_logical_expression := { nary_logical_operator : [ query_expression,...] }
unary_logical_operator := "$not"
nary_logical_operator := "$and" | "$or" | "$all" | "$any"

comparison_expression := relational_expression | array_comparison_expression
relational_expression := binary_relational_expression |
                         nary_relational_expression |
                         regex_match_expression

binary_relational_expression := field_comparison_expression |
                                value_comparison_expression
field_comparison_expression := { "field": <field>,
                                 op: binary_comparison_operator,
                                 "rfield": <field> }
value_comparison_expression := { "field": <field>,
                                 op: binary_comparison_operator,
                                 rvalue: <value> }
binary_comparison_operator := "=" | "!=" | "<" | ">" | "<=" | ">=" |
                              "$eq" | "$neq" | "$lt" | "$gt" | "$lte" | "$gte"

nary_relational_expression := nary_value_relational_expression |
                              nary_field_relational_expression

nary_value_relational_expression := { "field": <field>,
                                       op: nary_comparison_operator,
                                       values: value_list_array } 

nary_field_relational_expression := { "field": <field>,
                                       op: nary_comparison_operator,
                                       "rfield": <array_field> } 
                              
nary_comparison_operator := "$in" "$not_in" "$nin"

regex_match_expression := { "field": <field>, regex: <pattern>,
                            caseInsensitive: false,
                            extended: false,
                            multiline: false,
                            dotall: false }

array_comparison_expression := array_contains_expression |
                               array_match_expression
array_contains_expression := { array: <field>,
                               contains: "$any" | "$all" | "$none",
                               values: value_list_array }
array_match_expression := { array: <field>,
                            elemMatch: query_expression }
value_list_array := [ value1, value2, ... ]
```
Examples:

Search documents with login=someuser:
```javascript
    {
        "field": "login",
        "op": "=",
        "rvalue": "someuser"
    }
```
Search documents whose firstname is not equal to lastname:
```javascript
    {
        "field": "firstname",
        "op": "$ne",
        "rfield": "lastname"
    }
```

'field' and 'rfield' can be array fields, or simple fields.
  * simpleField op arrayField evaluates to true if simpleField op arrayField[i] for all i
  * arrayField1 op arrayField2 evaluates to true if arrayField1[i] op arrayField2[i] for all i

Logical expressions:
```javascript
    {
        "$not": {
            "field": "firstname",
            "op": "$ne",
            "rfield": "lastname"
        }
    }
```
```javascript
    {
        "$and": [
            {
                "field": "firstname",
                "op": "$ne",
                "rfield": "lastname"
            },
            {
                "field": "login",
                "op": "=",
                "rvalue": "someuser"
            }
        ]
    }
```
Can use "$all" instead of "$and".

```javascript
    {
        "$or": [
            {
                "field": "firstname",
                "op": "$ne",
                "rfield": "lastname"
            },
            {
                "field": "login",
                "op": "=",
                "rvalue": "someuser"
            }
        ]
    }
```
Can use "$any" instead of "$or".

List of values:
```javascript
    {
        "field": "city",
        "op": "$in",
        "values": [
            "Raleigh",
            "Cary"
        ]
    }
```

```javascript
    {
        "field": "city",
        "op": "$in",
        "rfield": "citiesArrayField"
    }
```


Search for a login name, starting with a prefix, case insensitive:
```javascript
    {
        "field": "login",
        "regex": "prefix.*",
        "options": "i"
    }
 ```

### Array Queries

There are two types of array queries. An array contains query checks
if an array of primitive values contains some, all, or none of the
given values.

Search for a document where an array field contains "value1" and "value2"
```javascript
    {
        "array": "someArray",
        "contains": "$all",
        "values": [
            "value1",
            "value2"
        ]
    }
```

An array match query evaluates a nested search criteria for all
elements of an array. The array query evaluates to 'true' if at least
one of the array element matches the nested query. The result is
'false' if none of the array elements matches.The field names in the
nested query are evaluated with respect to the array elements.

Search for a document that contains an array with an object
element with "item" field equals 1.
```javascript
    {
        "array": "someArray",
        "elemMatch": {
            "field": "item",
            "op": "=",
            "rvalue": "1"
        }
    }

```

Note that a query decides whether a document will be included in the
result set or not. If only the matching array elements are required,
the search criteria used in the query should also be used in
projection.
