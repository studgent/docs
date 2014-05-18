
#### Datasources, CurlUtil and caching

We abstracted the retrieval of business/common objects from the dataprovider and put this logic in DataSources implementing the `IDataSource` interface (see: **Architectural design decisions → Business objects **). The datasources utilize `CurlUtil` to fetch or post data from/to a specific resource. The output from `CurlUtil` is parsed as JSON and mapped to their respective common object. For strings there is a small `JSONUtil` class that expands the `optString` method to correctly identify `null` objects.

##### CurlUtil

Because retrieving data from the web using GET and POST was used for many things, we decided to create this logic in a separate class. This class gives a generic way of posting/getting data to/from an online resource. 

##### Caching

The raw (JSON) output from `CurlUtil` is also saved as a file in the *Cache* directory with an expiration of 24h. This made sure once the app has loaded the required data, it could work offline and it also limits the data usage. 
Another positive effect was a speed improvement. The experience was so good there wasn't any strong reason to also put the common objects from the datasources in a SQLite database.