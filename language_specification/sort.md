# Sort
```javascript
sort := sort_key | [ sort_key, ... ]
sort_key := { "field": "$asc" | "$desc" }
```
Examples:

Sort by login ascending:
```javascript
    { "login":"asc" }
```
Sort by last update date descending, then login ascending
```javascript
    [ { "lastUpdateDate":"desc" }, { "login": "asc" }]
```
