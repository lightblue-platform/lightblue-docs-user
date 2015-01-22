# Create First Metadata

This section assumes:
* your lightblue deployment hostname is `BASE_URL`
* you have a copy of [country.json](https://raw.githubusercontent.com/lightblue-platform/openshift-lightblue-all/master/src/main/data/country.json) on your local filesystem in the `/tmp` directory

## Create via Command Line
You can create metadata with `curl` if the metadata is in a file on the filesystem.  The following command creates version `1.0.0` of the `country` entity.

```bash
curl -H Content-Type:application/json -X PUT ${BASE_URL}/rest/metadata/country/1.0.0 -d @/tmp/country.json
```

## Create via Metadata Management Application
There is also a management application provided with lightblue for maintaining metadata.

1. Go to `http://BASE_URL/app/metadata` in your browser
2. Select "Create Entity" from the "Action" drop down
3. Click the "Submit" button
4. Click the "View/edit JSON" link (to enable pasting raw JSON into the tool)
5. Paste the contents of `/tmp/country.json` into the text area
6. Click the "Save" button
