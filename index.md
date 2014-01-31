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

# Getting started with Elasticsearch {#Cover}

![](pictures/cover.jpg)

## Goal of this presentation

Skim the top of Elasticsearch and give you pointers on where to start and what not to ignore.

## What is Elasticsearch?

* A modern, distributed search engine based on [Apache Lucene](http://lucene.apache.org) using a dynamic data model
* A distributed aggregation engine
* A database excelling at unstructured data like text

## Quickstart

Downloads are found at [http://elasticsearch.org/downloads](http://www.elasticsearch.org/download).

~~~sh
$ curl https://download.elasticsearch.org/elasticsearch/elasticsearch/elasticsearch-0.90.10.zip
$ unzip elasticsearch-0.90.10.zip
$ cd elasticsearch-0.90.10
$ bin/elasticsearch -f
~~~

## Welcome


    [Tempus] version[0.90.5], pid[17392], build[c8714e8/2013-09-17T12:50:20Z]

## Your First Data

~~~json
{
  "id": 123,
  "name": "Florian Gilcher",
  "place": "Berlin",
  "birthdate": "1983-10-04T00:00:00+01:00",
  "interests": ["code", "data", "elasticsearch"],
  "age": 30
}
~~~

## What can I put in there?

ElasticSearch handles:

* Structured key-value data, with nesting
* Primitive types: Dates, Numbers, Strings
* Complex types: Objects, Arrays, Geo-Locations, IP-Adresses...

[Reference](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/mapping-types.html)

## Index

Let's put the stuff in the database.

~~~sh
$ export $HOST=http://localhost:9200
$ curl -XPOST $HOST/my_index/person/123 -d @person.json
{"ok":true,"_index":"my_index","_type":"person","_id":"123","_version":1}
~~~

This operation is part of the [Document API](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/docs.html) and is called "Index".

##  Retrieval

Here is how we get it back:

~~~sh
$ curl -XGET $HOST/my_index/person/123
~~~

## Search

We can also search for content:

~~~sh
$ curl -XGET $HOST/my_index/person/_search?q=florian&pretty
$ curl -XGET $HOST/my_index/_search?q=florian&pretty
~~~

This searches all fields!

## Result

~~~json
...
"hits" : [ {
    "_index" : "test",
    "_type" : "person",
    "_id" : "1",
    "_score" : 1.0,
    "_source" : {  "id": 123,  "name": "Florian Gilcher",  "place": "Berlin",  "birthdate": "1983-10-04T00:00:00+01:00",  "interests": ["code", "data", "elasticsearch"],  "age": 30}
  } ]
...
~~~

## Search using the DSL

Beyond debugging, the [Query DSL](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/query-dsl.html) is recommended for search queries:

~~~json
{
    "query": { "match" : { "name" : "florian" } }
}
~~~

## Queries and Filters

Queries can be constraint by filtering the data before running the query:

~~~json
{
    "query": { "match" : { "name" : "florian" } },
    "filter": { "range" : { "age" : { "gte": 25, "lte": 35 } } }
}
~~~

## Scoring in a nutshell

All results get ranked by a _score_. The _score_ represents how *good* a document matches by:

* Number of matching terms
* Score of subqueries
* Custom score boosts configured at search or index time

## Scoring can be influenced

There are multiple queries that can influence scoring.

The get go is the [function_score](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/query-dsl-function-score-query.html) query that can for example:

* Rate newer documents higher
* Rate closer geo-points higher
* Combinations of multiple factors

## Mappings

[Mappings](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/mapping.html) describe how incoming values are stored in the Lucene index.

Elasticsearch automatically detects the mapping of newly added types and fields.

## Example Mapping

~~~json
$ curl localhost:9200/test/person/_mapping?pretty
...
"person" : {
  "properties" : {
    "age" : { "type" : "long" },
    "birthdate" : { "type" : "date", "format" : "dateOptionalTime" },
    ...
    "name" : { "type" : "string" },
  }
}
~~~

## Analysis

Analysis is the step of breaking text data into terms that can be indexed.

Searches are also analyzed.

## What we end up with

Lucene builds a reverse index of your data.

| keyword        | documents   |
+----------------|-------------|
| florian        | 1           |
| gilcher        | 1,2         |
| felix          | 2           |

## How we get there

| step                 | Input                       | Output              |
+----------------------+-----------------------------|---------------------|
| Whitespace Tokenizer | "The quick fox"             | "The" "quick" "fox" |
| lowercase filter     | "The" "quick" "fox"         | "the" "quick" "fox" |
| stopword filter      | "the" "quick" "fox"         | "quick" "fox"       |
| synoym   filter      | "quick" "fox"               | "quick" "fast" "fox"|

A match query for `quick`, `fast` or `fox` will find this document.

## Analysis is important!

Getting analysis right is the difference between a good and a bad search.

## Facets (< 1.0)

[Facets](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/search-facets-histogram-facet.html) are the old form of aggregating data on elasticsearch.

~~~json
{
    "query" : { "match_all" : {} },
    "facets" : {
        "sales" : {
            "date_histogram" : {
                "field" : "value",
                "interval" : "day"
            }
        }
    }
}
~~~

## Aggregations (1.0)


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

## Distribution

Distribution works at an index-level by breaking the index into shards and distributing it over the cluster.

## Node

A node is a running instance of Elasticsearch. A production system should at least consist of 2 nodes.

## Elasticsearch Index

An Index stores documents. It consists of Shards. The "Elasticsearch index" is _not_ a Lucene index.

## Shard / Replica

An index is split into multiple shards (5 per default). For reliability and performance reasons, each shard is copied multiple times. These copies are called "replica".

## Distribution

The shards are distributed using the following strategy:

* All shards need to be present on at least one node
* All replica are deployed on nodes where no other copy of the shard resides

## What do Replicas give?

Replicas allow two things:

* Failure of nodes - a copy on another shard can take over
* Speed of search operations: more nodes can participate

## Pitfalls

The main pitfalls when starting Elasticsearch are:

* Ignoring analysis until it is too late
* Not getting intimate with the query language
* Not understanding that Elasticsearch is meant to be distributed

## Things to definitely poke around with

* [Kibana](http://kibana.org/), making aggregations easy
* [Inquisitor](https://github.com/polyfractal/elasticsearch-inquisitor), for developing Analyzers
* [Marvel](http://www.elasticsearch.com/marvel), Elasticsearchs aggregation tool (commercial)
* [Logstash](http://logstash.net/), Log analysis, powered by ElasticSearch

## Photo Credit

Cover: Bonsai Rock, Lake Tahoe

http://www.flickr.com/photos/tensafefrogs/4513403767/