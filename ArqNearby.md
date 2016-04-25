| **Function name**| **Description** |
|:-----------------|:----------------|
| ext:nearby(lat lon limit) | find nodes nearest to the given latitude and longitude |
| ext:nearby(?var) | find nodes nearest to ?var.  This assumes ?var has geo:lat and geo:long properties (wc3 geo) |

# Introduction #
Much of what we call the semanic web has a strong geo spatial component.

# geo:nearby #
The geo:nearby SPARQL extension provides users with the ability to
query nodes based on their nearness to a point.  Typical usage will look
something like this:

```
PREFIX geo: <java:org.geospatialweb.arqext.>
SELECT ?n WHERE { ?s geo:nearby(51.45 -2.5833333 30) ....
```

The special prefix, java:org.geospatialweb.arqext. , allows ARQ to see the "nearby" extension class.