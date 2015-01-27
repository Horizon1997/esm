Elasticsearch Dumper
===

Example: ```elasticsearch-dumper -s source:9200 -d destination:9200 -i index1,index2```

```
Application Options:
  -s, --source=   Source elasticsearch instance
  -d, --dest=     Destination elasticsearch instance
  -c, --count=    Number of documents at a time: ie "size" in the scroll request (100)
  -t, --time=     Scroll time (1m)
      --settings  Copy sharding and replication settings from source (true)
  -f, --force     Delete destination index before copying (false)
  -i, --indexes=  List of indexes to copy, comma separated (_all)
```

NOTES:
==

1. Source and Destination are required
1. Copies using the [_source](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/mapping-source-field.html) field in elasticsearch. If you have made modifications to it (excluding fields) they will not be indexed on the destination host.
1. Ports are required, otherwise 80 is the assumed port
1. --settings is a toggle on weather we should copy replication and sharding settings for the indexes. This is the default, and replication and sharding are the only settings copied.
1. --force will delete indexes on the destination host. Otherwise an error will be returned if the index exists
1. --time is the [scroll time](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/search-request-scroll.html#scroll-search-context) passed to the source host, default is 1m. This is a string in es's format.
1. --count is the [number of documents](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/search-request-scroll.html#scroll-scan) that will be requested and the number of documents that will be flushed at a time. Note that this depends on the number of shards (ie: size of 10 on 5 shards is 50 documents)
1. --indexes is a comma separated list of indexes to copy

BUGS:
==

1. It will not do anything special when copying the _id (copies _id from source host). If _id is remapped this probably wont do what you want.
1. Should check if the bulk index requests starts getting large (in bytes), and force a flush if that is the case. Right now we silently fail if elasticsearch refuses a large request.
1. Should assume a default port of 9200