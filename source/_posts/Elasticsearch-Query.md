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


## ES集群优化
- https://blog.csdn.net/Gamer_gyt/article/details/62217053

## Metrics Aggregations

### Avg/Max/Min Aggregation

- By aggregation API
	```
	curl -X POST "localhost:9200/exams/_search?size=0&pretty" -H 'Content-Type: application/json' -d'
	{
		"aggs" : {
			"avg_grade" : { "avg" : { "field" : "grade" } },
			"max_price" : { "max" : { "field" : "price" } },
			"min_price" : { "min" : { "field" : "price" } },
			"hat_prices" : { "sum" : { "field" : "price" } },
			"types_count" : { "value_count" : { "field" : "type" } },
			"review_average": { "avg": { "field": "rating" } },
			"review_variability": { "median_absolute_deviation": { "field": "rating" } },
			"load_time_outlier" : { "percentiles" : { "field" : "prediction" }
    }
		}
	}
	'

	Response:
	{
		...
		"aggregations": {
			"avg_grade": {
				"value": 75.0
			},
			"max_price": {
				"value": 100.0
			},
			"min_price": {
				"value": 10.0
			},
			"hat_prices": {
				"value": 450.0
			},
			"types_count": {
				"value": 7
			},
			"load_time_outlier" : {
				"values" : {
					"1.0" : 1.2999999523162842,
					"5.0" : 1.3249999582767487,
					"25.0" : 20.799999237060547,
					"50.0" : 30.100000381469727,
					"75.0" : 31.200000762939453,
					"95.0" : 31.69999933242798,
					"99.0" : 31.799999237060547
				}
			},
		}
	}
	```
- By script
	```
	curl -X POST "localhost:9200/exams/_search?size=0&pretty" -H 'Content-Type: application/json' -d'
	{
		"aggs" : {
			"avg_corrected_grade" : {
				"avg" : {
					"field" : "grade",
					"script" : {
						"lang": "painless",
						"source": "_value * params.correction",
						"params" : {
							"correction" : 1.2
						}
					}
				}
			},
			"max_prediction_in_euros" : {
				"max" : {
					"field" : "prediction",
					"script" : {
						"source" : "_value * params.conversion_rate",
						"params" : {
							"conversion_rate" : 1.2
						}
					}
				}
			},
			"min_price_in_euros" : {
				"min" : {
					"field" : "price",
					"script" : {
						"source" : "_value * params.conversion_rate",
						"params" : {
							"conversion_rate" : 1.2
						}
					}
				}
			},
			"square_hats" : {
				"sum" : {
					"field" : "price",
					"script" : {
						"source": "_value * _value"
					}
				}
			},
			"load_time_outlier" : {
				"percentiles" : {
					"script" : {
						"lang": "painless",
						"source": "doc['load_time'].value / params.timeUnit", 
						"params" : {
							"timeUnit" : 1000   
						}
					}
				}
			},
			"review_variability": {
				"median_absolute_deviation": {
					"script": {
					"lang": "painless",
					"source": "doc['rating'].value * params.scaleFactor",
					"params": {
						"scaleFactor": 2
					}
					}
				}
			}
		}
	}
	'	
	```
### Weighted Avg Aggregation
- By API
	```
	curl -X POST "localhost:9200/exams/_search?pretty" -H 'Content-Type: application/json' -d'
	{
		"size": 0,
		"aggs" : {
			"weighted_grade": {
				"weighted_avg": {
					"value": {
						"field": "grade"
					},
					"weight": {
						"field": "weight"
					}
				}
			}
		}
	}
	'
	```
- By Script
	```
	curl -X POST "localhost:9200/exams/_search?pretty" -H 'Content-Type: application/json' -d'
	{
		"size": 0,
		"aggs" : {
			"weighted_grade": {
				"weighted_avg": {
					"value": {
						"script": "doc.grade.value + 1"
					},
					"weight": {
						"script": "doc.weight.value + 1"
					}
				}
			}
		}
	}
	'
	```
### Cardinality Aggregation
- By API
	```
	curl -X POST "localhost:9200/sales/_search?size=0&pretty" -H 'Content-Type: application/json' -d'
	{
		"aggs" : {
			"type_count" : {
				"cardinality" : {
					"field" : "type",
					"precision_threshold": 100 
				}
			}
		}
	}
	'
	```
- By script
	```
	curl -X POST "localhost:9200/sales/_search?size=0&pretty" -H 'Content-Type: application/json' -d'
	{
		"aggs" : {
			"type_promoted_count" : {
				"cardinality" : {
					"script": {
						"lang": "painless",
						"source": "doc[\u0027type\u0027].value + \u0027 \u0027 + doc[\u0027promoted\u0027].value"
					}
				}
			}
		}
	}
	'
	```
### Extendend Stats Aggregation
- By API
	```
	curl -X GET "localhost:9200/exams/_search?pretty" -H 'Content-Type: application/json' -d'
	{
		"size": 0,
		"aggs" : {
			"grades_stats" : {
				"extended_stats" : {
					"field" : "grade",
					"sigma" : 2
				}
			}
		}
	}
	'

	Response:
	{
		...

		"aggregations": {
			"grades_stats": {
			"count": 2,
			"min": 50.0,
			"max": 100.0,
			"avg": 75.0,
			"sum": 150.0,
			"sum_of_squares": 12500.0,
			"variance": 625.0,
			"std_deviation": 25.0,
			"std_deviation_bounds": {
				"upper": 125.0,
				"lower": 25.0
			}
			}
		}
	}
	```
- By script
	```
	curl -X GET "localhost:9200/exams/_search?pretty" -H 'Content-Type: application/json' -d'
	{
		"size": 0,
		"aggs" : {
			"grades_stats" : {
				"extended_stats" : {
					"field" : "grade",
					"script" : {
						"lang" : "painless",
						"source": "_value * params.correction",
						"params" : {
							"correction" : 1.2
						}
					}
				}
			}
		}
	}
	'
	```
### Geo Bounds Aggregation
- By API
	```
	POST /museums/_search?size=0
	{
		"query" : {
			"match" : { "name" : "musée" }
		},
		"aggs" : {
			"viewport" : {
				"geo_bounds" : {
					"field" : "location", 
					"wrap_longitude" : true 
				}
			}
		}
	}

	POST /museums/_search?size=0
	{
		"aggs" : {
			"cities" : {
				"terms" : { "field" : "city.keyword" },
				"aggs" : {
					"centroid" : {
						"geo_centroid" : { "field" : "location" }
					},
					"viewport" : {
					"geo_bounds" : {
						"field" : "location", 
						"wrap_longitude" : true 
					}
			}
				}
			}
		}
	}

	Response:
	{
		...
		"aggregations" : {
			"cities" : {
				"doc_count_error_upper_bound" : 0,
				"sum_other_doc_count" : 0,
				"buckets" : [
					{
						"key" : "Amsterdam",
						"doc_count" : 3,
						"viewport" : {
							"bounds" : {
							"top_left" : {
								"lat" : 52.374080987647176,
								"lon" : 4.901617951691151
							},
							"bottom_right" : {
								"lat" : 52.36921898089349,
								"lon" : 4.91472196765244
							}
							}
						},
						"centroid" : {
							"location" : {
							"lat" : 52.371655656024814,
							"lon" : 4.909563297405839
							},
							"count" : 3
						}
						},
						{
						"key" : "Paris",
						"doc_count" : 2,
						"viewport" : {
							"bounds" : {
							"top_left" : {
								"lat" : 48.86111099738628,
								"lon" : 2.3269999679178
							},
							"bottom_right" : {
								"lat" : 48.85999997612089,
								"lon" : 2.3363889567553997
							}
							}
						},
						"centroid" : {
							"location" : {
							"lat" : 48.86055548675358,
							"lon" : 2.3316944623366
							},
							"count" : 2
						}
						},
						{
						"key" : "Antwerp",
						"doc_count" : 1,
						"viewport" : {
							"bounds" : {
							"top_left" : {
								"lat" : 51.22289997059852,
								"lon" : 4.40519998781383
							},
							"bottom_right" : {
								"lat" : 51.22289997059852,
								"lon" : 4.40519998781383
							}
							}
						},
						"centroid" : {
							"location" : {
							"lat" : 51.22289997059852,
							"lon" : 4.40519998781383
							},
							"count" : 1
						}
					}
				]
			}
		}
	}
	```
### Scripted Metric Aggregation
```
curl -X POST "localhost:9200/ledger/_search?size=0&pretty" -H 'Content-Type: application/json' -d'
{
    "query" : {
        "match_all" : {}
    },
    "aggs": {
        "profit": {
            "scripted_metric": {
                "init_script" : "state.transactions = []", 
                "map_script" : "state.transactions.add(doc.type.value == 'sale' ? doc.amount.value : -1 * doc.amount.value)",
                "combine_script" : "double profit = 0; for (t in state.transactions) { profit += t } return profit",
                "reduce_script" : "double profit = 0; for (a in states) { profit += a } return profit"
            }
        }
    }
}
'

```