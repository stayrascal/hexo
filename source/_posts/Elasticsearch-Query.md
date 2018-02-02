---
title: Elasticsearch-Query
date: 2018-01-27 15:07:03
tags:
---

## Elasticsearch Light Weight Search

### +name:(mary john) +date:>2014-09-10 +(aggregations geo)
- name 字段中包含 mary 或者 john
- date 值大于 2014-09-10
- _all_ 字段包含 aggregations 或者 geo


## Elasticsearch API

### Health Check
- List all indices: /_cat/indices?v
- Cluster Health: /_cat/health?v
- Nodes Status: /_cat/nodes?v

### Create an Index
- Create an Index:  `PUT` /customer?pretty
- Query a Document: `PUT` /customer/doc/1?pretty

	```
	{
		"name": "John Doe"
	}
	```
- Delete an Index: `DELETE` /customer?pretty
- Update Documents: `POST` /customer/doc/1?_update?pretty

    ```
    {
        "doc": { "name": "Jane Doe"}
    }
    ```
- Update Documents with scripts: `POST` /customer/doc/1/_update?pretty

	```
	{
		"scripts": "ctx._source.age += 5"
		"upsert": {
			"age" : 0
		}
	}
	```
- Delete Doucments: `DELETE` /customer/doc/2?pretty
- Batch Processing: `POST` /customer/doc/_bulk?pretty
	
	```
	{"index":{"_id":"1"}}
	{"name": "John Doe" }
	{"index":{"_id":"2"}}
	{"name": "Jane Doe" }
	```
### Search API

#### Sending through the REST request URI
- return all bank documents: `GET` /bank/_search?q=*&sort=account_number:asc&pretty
- Response Filtering: 
	* `GET` /_search?q=elasticsearch&filter_path=took,hits.hits._id,hits.hits._score
	* `GET` /_cluster/state?filter_path=metadata.indices.*.stat*
	* `GET` /_cluster/state?filter_path=routing_table.indices.**.state
	* `GET` /_count?filter_path=-_shards
	* `GET` /_cluster/state?filter_path=metadata.indices.*.state,-metadata.indices.logstash-*
	*  `GET` /_cluster/state?filter_path=metadata.indices.*.state,-metadata.indices.logstash-*
- Flat Settings: `GET` /twitter/_settings?flat_settings=true
- Enable stack traces: `POST` /twitter/_search?size=surprise_me&error_trace=true
- Check if exist: `HEAD` /twitter/tweet/0
- Source filtering: `GET` /twitter/tweet/0?_source_include=*.id&_source_exclude=entities
- Delete Index: `DELETE` /twitter/tweet/1?timeout=5m


### Sending through the REST request body
- return all bank documents: `GET` /bank/_search

	```
	{
		"query": { "match_all": {} },
		"size": 10,
		"form": 0,
		"_source": ["accunt_number", "balance" ], # the response only have these two fileds
		"sort": [
			{ "account_number": "asc" }
		]
	}
	```

- return bank documents: `GET` /bank/_search

	```
	{
		"query": { 
			"match": { 
				"account_number": 20,
				"address": "mill lane" # address contains "mill" or "lane",
			} ,
			"match_phrase": {
				"addresss": "mill lane" # address contains "mill lane"
			}
		}
	}
	```
- Compose smaller: `GET` /bank/_search

	```
	{
		"query": {
			"bool": {
				"must": { # address contains "mill" and "lane"
					{ "match": { "address": "mill" } },
					{ "match": { "address": "lane" } }
				},
				"should": { # address contains "mill" or "lane"
					{ "match": { "address": "mill" } },
					{ "match": { "address": "lane" } }
				},
				"must_not": { # address contains neither "mill" nor "lane"
					{ "match": { "address": "mill" } },
					{ "match": { "address": "lane" } }
				}
			} 
		}
	}
	```
- filter in query: `GET` /bank/_search

	```
	{
	  "query": {
	    "bool": {
	      "must": { "match_all": {} },
	      "filter": {
	        "range": {
	          "balance": {
	            "gte": 20000,
	            "lte": 30000
	          }
	        }
	      }
	    }
	  }
	}
	```
- Aggregations: `GET` /bank/_search

	```
	{
	  "size": 0,
	  "aggs": {
	    "group_by_state": {
	      "terms": {
	        "field": "state.keyword",
	        "order": {
	          "average_balance": "desc"
	        }
	      },
	      "aggs": {
	        "average_balance": {
	          "avg": {
	            "field": "balance"
	          }
	        }
	      }
	    }
	  }
	}
	
	{
	  "size": 0,
	  "aggs": {
	    "group_by_age": {
	      "range": {
	        "field": "age",
	        "ranges": [
	          {
	            "from": 20,
	            "to": 30
	          },
	          {
	            "from": 30,
	            "to": 40
	          },
	          {
	            "from": 40,
	            "to": 50
	          }
	        ]
	      },
	      "aggs": {
	        "group_by_gender": {
	          "terms": {
	            "field": "gender.keyword"
	          },
	          "aggs": {
	            "average_balance": {
	              "avg": {
	                "field": "balance"
	              }
	            }
	          }
	        }
	      }
	    }
	  }
	}
	```
- Delete By Query API

	```
	{
	  "query": { 
	    "match": {
	      "message": "some message"
	    }
	  }
	}
	```

### Filed datatypes

#### Core datatypes
- String: text, keyword
- Numberic datatypes: long, integer, short, byte, double, float, half_float, scaled_float
- Date datatype: date
- Boolean datatype: boolean
- Binary datatype: binary
- Range datatypes: integer_range, float_range, long_range, double_range, date_range 

#### Complex datatypes
- Array datatype: Array support does not require a dedicated type
- Object datatype: object for single JSON objects
- Nested datatype: nested for arrays of JSON objects

#### Geo datatypes
- Geo-point datatypes: geo_point for lat/lon points
- geo-Shape datatype: geo_shape for complex shapes like polygons

#### Spedicalised datatypes
- IP datatype: ip for IPv4 and IPv6 addresses
- Completion datatype: completion to provide auto-complete suggestions
- Token count datatype: token_count to count the number of tokens in a string
- mapper-murmur3: murmur3 to compute hashes of values at index-time and store them in the index
- Percolator type: Accepts queries from the query-dsl
- join datatype: Defines parent/child relation for documents within the same index