# Insert Data

This section assumes:
* your lightblue deployment hostname is `BASE_URL`
* you have a copy of [country-data.json](https://raw.githubusercontent.com/lightblue-platform/openshift-lightblue-all/master/src/main/data/country-data.json) on your local filesystem in the `/tmp` directory


## Create via Command Line
You can create metadata with `curl` if the metadata is in a file on the filesystem.  The following command creates some data for version `1.0.0` of the `country` entity.

```bash
curl -H Content-Type:application/json -X PUT ${BASE_URL}/rest/data/country/1.0.0 -d @/tmp/country-data.json
```

## Create via Data Management Application
Coming soon!
