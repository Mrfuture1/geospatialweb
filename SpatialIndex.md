# Introduction #

Before using the geospatial [ARQ](http://jena.sourceforge.net/ARQ/) extensions first create an index of your RDF model.

# Details #
This snippet creates an index, that by default will assume you are asking it to find RDF nodes with the w3c's geo:lat and geo:long properties.

```
OntModel m = ...
Indexer i = Indexer.createDefaultIndexer();
i.createIndex(m);
```
The first line creates a jena model.  There are many ways so we'll assume you're familiar with Jena.  Line 2 creates a default index.  This indexer is memory based.
Finally, line 3 instructs the Indexer to create an index against Jena model "m".  The indexer will look at your model and find all nodes with both a geo:lat and geo:long property and add them to the index.  If you want to only index a subset of your graph there's a way, and we'll look at that later.  Now we are ready to perform a sparql QUERY:

```
1: String queryString = "PREFIX : <http://example.org/>"
	+ "PREFIX geo: <http://www.w3.org/2003/01/geo/wgs84_pos#>\n "
	+ "PREFIX ext: <java:org.geospatialweb.arqext.>\n\n "
	+ "SELECT ?subject ?c" 
	+ "WHERE { ?c a :City . ?subject ext:nearby( ?c 20 )}";
2: Query query = QueryFactory.create(queryString);
3: QueryExecution qexec = QueryExecutionFactory.create(query, m);
4: Geo.setContext(qexec, i);
5: try {
6:    ResultSet results = qexec.execSelect();
7:    for (; results.hasNext();) {...
```

In our query we have three namespaces.  Assuming you are familiar with SPARQL the only thing that should stand out is the <java:...> namespace.  That tells ARQ where to find the extension function.  Within the query the function is used in this triple:
```
?subject ext:nearby( ?c 20 )
```
In English, find nodes near city ?c, limiting the results to 20.  The last important item outside standard ARQ is line 4.  Here we set the the index into our query context.  Only then is the extension function able to find the index.  Note that you can create several indexes and use them as needed.

As stated above, the indexer defaults to indexing nodes with geo:lat and geo:long, however, you can change how the indexer behaves by providing it with a custome SPARQL query.  This is the default query:
```
PREFIX geo: <http://www.w3.org/2003/01/geo/wgs84_pos#>
SELECT ?s ?lat ?lon
WHERE {?s geo:lat ?lat . ?s geo:long ?lon}
```

You may want to restrict the index to a certain class.  This could be done by adding the restriction to the default query.  You may also wish to index nodes with your own geo properties.  Basically you can do anything that is valid SPARQL, with the restriction that your SELECT clause returns the node, the latitude, and the longitude.

```
String customeQuery = ...
i.createIndex(m, customeQuery); 
```