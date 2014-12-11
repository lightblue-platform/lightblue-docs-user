# Create a new Version

One of the powerful things about lightblue is the ability to deploy multiple versions of an entity at the same time without changing any code.  In this section we will create a new version of the `country` entity that includes a new optional field `continentName`.



## Edit Metadata
Open `/tmp/country.json` in your favorite editor and add a field after `iso3Code` so it looks like this:

```javascript
...
            "iso3Code": {
                "type": "string",
                "constraints": {
                    "required": true
                }
            },
            "continentName": {
                "type": "string"
            }
        }
    }
}

```

## Update Metadata
In this section you will create version `1.1.0` of the `country` entity.

## Before lightblue 1.2.0
If you're using a version of lightblue older than 1.2 then you have to follow these steps:
1. Copy `/tmp/country.json` to `/tmp/country-schema.json`
2. Edit `/tmp/country-schema.json`
3. Make the value of the `schema` element the top level document
    1. remove `entityInfo`
    2. remove top level currely braces
    3. remove text "schema":
4. Save the file

Create a new schema with `curl`:
```bash
curl -H Content-Type:application/json -X PUT ${BASE_URL}/rest/metadata/country/schema=1.1.0 -d @/tmp/country-schema.json
```

## lightblue 1.2.0+
NOTE: this section relies on a [feature](https://github.com/lightblue-platform/lightblue-core/issues/55) released with version 1.2.0.

Update metadata with `curl`:
```bash
curl -H Content-Type:application/json -X PUT ${BASE_URL}/rest/metadata/country/1.1.0 -d @/tmp/country.json
```
