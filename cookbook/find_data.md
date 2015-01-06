# Find Data

## Find via Command Line
Again, using `curl` you can interact easily with the lightblue service.

### Simple Query: US
Find the country `US` using the simple query API.
```bash
curl ${BASE_URL}/rest/data/find/country/1.0.0?Q=iso2Code:US
```

### Simple Query: CA
Find the country `CA` using the simple query API.
```bash
curl ${BASE_URL}/rest/data/find/country/1.0.0?Q=iso2Code:CA
```

### Complex Query: US
Find the country `US` using the full query API.
```bash
curl -H Content-Type:application/json -X POST  -d '{
  "objectType":"country",
  "projection": { "field":"*","recursive":1},
  "sort" : { "iso3Code":"$asc"},
  "query":  { "field":"iso2Code", "op":"$eq", "rvalue":"US" },
  "range" : [0,10]
}' ${BASE_URL}/rest/data/find/country/1.0.0
```

## Find via Data Management Application
Coming soon!
