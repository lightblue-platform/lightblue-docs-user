# Testing Multiple Versions

In this section we'll update data created with version `1.0.0` of `country` to add a value for `continentName` value and show how clients that continue to use the older `1.0.0` version are not impacted.

## Set continentName
Since we only have two contries, US and CA, we'll issue an update to set all the documents.

```bash
curl -H Content-Type:application/json -X POST  -d '{
  "objectType":"country",
  "projection": { "field":"*","recursive":1},
  "query": { "field": "objectType", "op": "=", "rvalue": "country" },
  "update": [ { "$set": {"continentName": "North America"} } ]
}' ${BASE_URL}/rest/data/update/country/1.1.0
```

## Verify version 1.0.0
We'll use a simple query and project fields `iso2Code` and `continentName`.
* version 1.0.0 still works
* client asking for a field it cannot access doesn't break
* version 1.0.0 does not see the new `continentName` field

```bash
curl ${BASE_URL}/rest/data/find/country/1.0.0?Q=iso2Code:US\&P=_id:1,continentName:1
```

## Verify version 1.1.0
The same query is used to verify version `1.1.0` by simply changing the version in the URL.
* version 1.1.0 works
* client sees field `continentName`

```bash
curl ${BASE_URL}/rest/data/find/country/1.1.0?Q=iso2Code:US\&P=_id:1,continentName:1
```
