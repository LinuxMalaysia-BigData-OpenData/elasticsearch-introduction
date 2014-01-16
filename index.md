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

# Getting started with ElasticSearch {#Cover}

![](pictures/cover.jpg)

## What is ElasticSearch?

* A modern, distributed search engine based on [Apache Lucene](http://lucene.apache.org)
* A distributed aggregation engine
* A database excelling at unstructured data like text

## Quickstart

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

## Photo Credit

Cover: Bonsai Rock

http://www.flickr.com/photos/tensafefrogs/4513403767/

## ![](http://shwr.me/pictures/logo.svg) [See more on GitHub](https://github.com/shower/shower/)
{:.shout #SeeMore}
