# Update
```javascript
update_expression := partial_update_expression |
                    [ partial_update_expression,...]
partial_update_expression := primitive_update_expression |
                             array_update_expression
primitive_update_expression := { $set : { path : rvalue_expression , ...} } |
                               { $unset : path } |
                               { $unset :[ path, ... ] }
                               { $add : { path : rvalue_expression, ... } }
rvalue_expression := value | { $valueof : path } | {}

array_update_expression := { $append : { path : rvalue_expression } } |
                           { $append : { path : [ rvalue_expression, ... ] }} |
                           { $insert : { path : rvalue_expression } } |
                           { $insert : { path : [ rvalue_expression,...] }} |
                           { $foreach : { path : update_query_expression,
                                         $update : foreach_update_expression } }
update_query_expression := $all | query_expression
foreach_update_expression := $remove | update_expression
```

Modifications are executed in the order they're given, and effects are
visible to subsequent operations immediately. For instance, to remove
the first two elements of an array, use:
```javascript
    { "$unset" : [ "arr.0","arr.0" ] }
```

### Primitive updates
```javascript
     { "$set" : { "path" : value } }
     { "$set" : { "path" : { "$valueof" : field } }
     { "$unset" : "path" }  (array index is supported, can be used to
                           remove elements of array)
     { "$add" : { "path" : number } }
     { "$add" : { "path" : { "$valueof" : pathToNumericField } }
```

### Array updates:
```javascript
     { "$append" : { "pathToArray" : [ values ] } } (values can be empty
                      objects (extend array with a  new element)

     { "$append" : { "pathToArray" : value } }

     { "$insert" : { "pathToArray.n" : [ values ] } }
     { "$insert" : { "pathToArray.n" : value } } (index (n) can be negative)
```

Assuming that value(s) is inserted at the given index and the item at
index and items after are shifted down

Negative index values can be used to address array elements from the end of the array.
Using this, it is possible to append and initialize object array elements.

```javascript
   [ { "$append" : { "someArray" : {} }},
     { "$set" : { "someArray.-1.someValue" : 1,
                  "someArray.-1.otherValue" : "value" } } ]
```

Above example first appends an empty object to the field "someArray", then sets
values in that element. The index -1 refers to the newly added element.


### Updating array elements:
```javascript
  { "$foreach" : { "pathToArray" : query_expression,
                   "$update": update_expression } }
```

The query_expression determines the elements that will be
updated. query_expression can be $all. update_expression can be
$remove.

#### Examples updating simple fields

```javascript
{ "$set": { "x.y.z" : newValue } }
```
Update field x.y.z. x and y are objects and z is a value.

---

```javascript
{ "$unset" : "x.y." }
```
Remove x.y.z from doc.
* x and y are objects.
* z can be a value, object, or array.
* x and y are not removed from the document, only z is removed

---

```javascript
{ "$add": { "x.y.z" : number } }
```
$add is similar to $set

#### Examples of $foreach

```javascript
{ "$foreach" : { "x.y.*.z" : "$all", "$update": ... }  }
```
Select all elements of the array x.y.*.z. Here, y is also an array.

---

```javascript
{ "$foreach" : { "x.y.*.z" : { "field" : "x.y.*.z", "op":"$gt","rvalue":5 },
                 "$update":...  }}
```
Select all elements of "x.y.*.z" that are greater than 5.

---

```javascript
{ "$foreach" : { "x.y.*.z" : { "field" : "x.y.*.z.w", "op":"$eq","rfield":"k" },
                               "$update":... } }
{ "$foreach" : { "x.y.*.z" : { "field" : "$this.w", "op":"$eq","rfield":"k" },
                               "$update":... } }
```
Select all elements of "x.y.*.z" where the field 'w' is
equal to the top-level field 'k'.

`$update` specifies how each matching array element will be updated.
The update expression itself may contain a $foreach.

---

```javascript
 { "$foreach" : { "x.y.*.z" : "$all",
                  "$update": { "$set" : { "$this" : 1 } } }
```
Set all elements to 1

---

```javascript
{ "$foreach" : { "x.y.*.z" : ...,
                 "$update" : { "$set" : { "$this.k" : "blah" } } }
```
Set field 'k' in matching elements to 'blah' (".k" means
"k relative to current context" )

---

```javascript
{ "$foreach" : { "x.y.*.z" : ..... "$update" : {
           "$foreach" : { "$this.arr" : { "field" : "$this.p", "op":"$eq","rvalue":1},

           "$update" : { "$set" : { "$this.v" : { "$valueof":"k"}}}}}}}
```
For each element of ```x.y.*.z``` where ```x.y.*.z.arr.p=1``` set ```x.y.*.z.arr.v```
to the value of ```k``` (k is from root of doc) .
