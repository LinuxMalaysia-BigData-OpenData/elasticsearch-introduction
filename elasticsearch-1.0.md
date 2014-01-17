---

layout: bright

style: |

    #Cover h2 {
        margin:30px 0 0;
        color:#FFF;
        text-align:center;
        font-size:70px;
        }
    #Cover p {
        margin:10px 0 0;
        text-align:center;
        color:#FFF;
        font-style:italic;
        font-size:20px;
        }
        #Cover p a {
            color:#FFF;
            }
    #Picture h2 {
        color:#FFF;
        }
    #SeeMore h2 {
        font-size:100px
        }
    #SeeMore img {
        width:0.72em;
        height:0.72em;
        }
---

# What's new in ElasticSearch 1.0 {#Cover}

![](pictures/cover.jpg)

## Federated Search

ElasticSearch 1.0 ships with a new [Tribe Node](), allowing to search over multiple _clusters_.

It acts as a federated client to multiple seperate ES clusters.

This feature is experimental.

## Cat API

The cat API is one of the most important additions to ElasticSearch since version its inception.

## Cat API

~~~sh
$ curl 'localhost:9200/_cat/'
=^.^=
~~~

## Cat API, for real

The cat API gives a human-readable overview of all important things:

~~~sh
$ curl 'localhost:9200/_cat/shards?v'
index        shard prirep state      docs   store ip        node
testindex    2     p      STARTED       1   2.1kb 127.0.0.1 Andromeda
testindex    2     r      UNASSIGNED
testindex    0     p      STARTED       0     99b 127.0.0.1 Andromeda
testindex    0     r      UNASSIGNED
~~~

## Snapshot/Restore

ElasticSearch 1.0 comes with a Snapshot/Restore API.

## Creating a Snapshot

~~~sh
$ curl -XPUT 'http://localhost:9200/_snapshot/my_backup' -d '{
    "type": "fs",
    "settings": {
        "location": "/mount/backups/my_backup",
        "compress": true
    }
}'
~~~

## Available Types

* `fs`: A shared filesystem location all nodes have access to
* `url`: A readonly repository to download a previous snapshot from

## Backing up

~~~sh
$ curl -XPUT \
"localhost:9200/_snapshot/my_backup/snapshot_1?wait_for_completion=true"
~~~

## Get info about the Snapshot

~~~sh
$ curl -XGET "localhost:9200/_snapshot/my_backup/snapshot_1"
~~~

## Restore

~~~sh
$ curl -XPOST "localhost:9200/_snapshot/my_backup/snapshot_1/_restore"
~~~

## Delete

~~~sh
$ curl -XDELETE "localhost:9200/_snapshot/my_backup/snapshot_1"
~~~

## Not Shown

* Finegrained control of backed up indices
* Control over backup conditions
  - e.g. only if all primaries are up
* Backup of cluster state

## Aggregations

Facets are still a part of ElasticSearch.

> Nevertheless, facets are and should be considered deprecated and will likely be removed in one of the future major releases.

## Aggregations

Aggregations are split in 2 parts:

* Bucket aggregations (nestable)
* Metrics aggregations

## Aggregations

~~~json
{  "aggregations": {
        "range": {
            "date_range": {
                "field": "date",
                "format": "MM-yyy",
                "ranges": [ { "to": "now" }, { "from": "now" } ]
...
~~~

## Aggregation nesting

~~~json
{  "aggregations": {
        "range": { ... },
        "aggregations": {
          "monthly" : {
              "date_histogram" : {
                  "field" : "date",
                  "interval" : "1M",
...
~~~

## Metrics

~~~json
{
    "aggs" : {
        "min_price" : { "min" : { "field" : "price" } }
    }
}
~~~

Metrics can be used as the last nested aggregation as well.

## Small side-dishes

* `simple_query_string` query: `query_string`, but simpler and with a more lenient parser - throws no exceptions.
* ...`sloppy_arc` geo_calculation: a good middleground between speed and accuracy in distance calculation.
* ...`token_count` field: Stores the number of tokens instead of the tokens themselves after analysis.
* ...some advanced features.

## What's breaking?

....

## Photo Credit

Cover: Bonsai Rock, Lake Tahoe

http://www.flickr.com/photos/tensafefrogs/4513403767/