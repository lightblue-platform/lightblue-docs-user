# Data

* Descriptive name is the Header
* First paragraph is a short description of the API
* Request section contains info on what the request looks like
* Response section contains info on what the response looks like, including errors where applicable
* Example section contains a successful example and an unsuccessful example.


## Future Functionality
In the future we might implement the following, but have no plans at this time.

### PUT: Save Query
Save a query for reuse.  Will require some design, but possibly a query with some bind parameteres that can be used in the Find with Saved Query API.

### GET: Find with Saved Query
Uses a saved query and optional bind parameters instead of posting the full query every time.
