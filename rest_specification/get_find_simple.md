# GET: Find (Simple)

A simplified find API that takes just query parameters to do a simple search.

```
GET /data/{entity}?Q&P&S&R
```
```
GET /data/{entity}/{version}?Q&P&S&R
```

where Q represents the query, P represents the projection, S represents the sort expressions, and R is the range.

Query expression:
```
 q=field1:value1,value2[;field2:value...]
```
Only value equivalence is supported.

Projection expression:
```
  p=field2:1,field3:0,...
```
:1, included, :0, excluded. Patterns can be used. :1r recursive inclusion, :0r recursive exclusion.

Sort expression:
```
  s=field1:a,field2:d,...
```
:a means ascending, :d means descending

Result Range:
```
from=<number>&to=<number>
```
If from is not given, result set starts from 0. If to is not given, all results are returned (up to a cap, given in configuration).
