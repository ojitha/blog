There are my personal experiments with Elastic Search.
The query with requery
```json
GET _search
{
  "query": {
    "match": {
      "parsedtext": {
        "operator": "or",
        "query": "nice buy joe"
      }
    }
  },
  "rescore":{
    "window_size":50,
    "query": {
      "rescore_query" : {
        "match": {
          "parsedtext": {
            "query" : "joe nice guy",
            "type" : "phrase",
            "slop" : 2
          }
        }
      },
      "query_weight" : 0.7,
      "rescore_query_weight" : 1.2
    }
  }
}
```

The final score calculated as:
final_score = query _score * query_weight + rescore_score * rescore_query_weight

To sort 
```json
GET test-index/test-type/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "price": {
        "order": "desc"
      }
    }
  ]
}
```
## Scrolling Query
This will give special cursor to uniquely iterate over the documents in ES.
```json
GET test-index/test-type/_search?scroll=10m&size=1
{
  "query": {
    "match_all": {}
  },
  "sort": [
    "_doc"
  ]
}
```
The result will give scrolling id.
To delete all the scrolling ids
```
DELETE _search/scroll/_all
```

You can use curl to delete only the scrolling id
```bash
curl -XDELETE localhost:9200/_search/scroll -d '
{
     "scroll_id" : ["DnF1ZXJ..."]
}'
```

## After search
This doesn't take memory. This feature has been introduced ES 5.x. This is important for scrolling or pagination results. 

```json
curl -XGET 'http://127.0.0.1:9200/test-index/test-type/_search?pretty=true' -d '
{
     "size": 1,
     "query": {
         "match_all" : {}
     },
     "sort": [
         {"price": "asc"},
         {"_uid": "desc"}
     ]
 }'
```
Then from the result, you can execute the following

```bash
curl -XGET 'http://127.0.0.1:9200/test-index/test-type/_search?pretty=true' -d '
 {
     "size": 1,
     "query": {
         "match_all" : {}
     },
     "search_after": [4.0, "test-type#1"],
     "sort": [
         {"price": "asc"},
         {"_uid": "desc"}
     ]
 }'
```

## Inner hits
ES returns the documents found in the search but not the nested documents, but using inner hits you can find the nested documents as well.

```json
GET test-index/test-type/_search
{
  "query": {
    "has_child": {
      "type": "test-type2",
      "query": {
        "term": {
            "value": "value1"
          
        }
      },
      "inner_hits": {}
    }
  }
}
```
## Suggest
Here the way to suggest
```json
GET test-index/_suggest
{
  "suggest1": {
    "text": "we find tester",
    "term": {
      "field": "parsedtext"
    }
  }
}
```

## Explain

```json
GET test-index/test-type/1/_explain
{
  "query" : {
     "term" : {"uuid": "11111"}
  }
}
```

## profiling
The time spent on query by searching or aggregating. But this will add the overhead to the computation.

```json
GET test-index/test-type/_search
{
  "profile": "true",
  "query": { 
  ...
  ...
```

## Update/Delete query

Here you can use either update or delete
```json
POST test-index/test-type/_update_by_query
{
  "query": {
    "match_all": {}
  }
}
```
in the first line# instead of `update` you can use `delete` to delete all the documents.

## bool query

There is a mandatory field which should be `must` , `must not` or `should`.
```json
GET test-index/test-type/_search
{
  "query": {
    "bool": {
      "must": [
        {"term": {"parsedtext": "joe"}}
      ]
    }
  }
}
```
Compare bool query with the term query below.

## Term Query

Here the basic `term` query example:

```json
GET test-index/test-type/_search
{
  "query": {
    "term": {
      "uuid": "33333"
    }
  }
}
```

This is for exact value matches of `filter` query using `term`. For example,

```json
GET test-index/test-type/_search
{
  "query": {
    "bool": {
      "filter": {
        "term": {
          "uuid": "33333"
        }
      }
    }
  }
}
```

Multi term queries can be from in two ways

- using Boolean query
- using multi-terms query

```json
GET test-index/test-type/_search
{
  "query": {
    "bool": {
      "should": [
        {"term": {"uuid": "33333"}},
        {"term": {"uuid": "11111"}}
      ]
    }
  }
}

GET test-index/test-type/_search
{
  "query": {
    "terms": {
      "uuid": ["33333", "11111"]
    }
  }
}
```

## Prefix Query

Here is the basic prefix query

```json
GET test-index/test-type/_search
{
  "query": {
    "prefix": {
      "uuid": "111"
    }
  }
}
```

## Wildcard Query

Only two wildcards are supported

- `*` - 0 or more characters
- `?` - Only one character

```json
GET test-index/test-type/_search
{
  "query": {
    "wildcard": {
      "uuid": "2222*"
    }
  }
}
```

## Regexp Query

```json
GET test-index/test-type/_search
{
  "query": {
    "regexp" :{
      "parsedtext": {
        "value": "j.*",
        "flags" : "INTERSECTION|COMPLEMENT"
      }
    }
  }
}
```

You can use regex to execute agains all the terms. Flags are optional: 

## Span Query

Span queries control the text tokens by their position. You can define

- Exact phrase query
- Exact fragment query
- Partial exact query

As shown in the following, first term should be `joe`.

```json
GET test-index/test-type/_search
{
  "query": {
    "span_first": {
      "match": {
        "span_term": {
          "parsedtext": "joe"
        }
      },
      "end": 3
    }
  }
}

```

Here the `span_or` query

```json
GET test-index/test-type/_search
{
  "query": {
    "span_or": {
      "clauses": [
        {"span_term": {"parsedtext": "nice"}},
        {"span_term": {"parsedtext": "cool"}}
      ]
    }
  }
}
```

Here use the prefix in the multi span:

```json
GET test-index/test-type/_search
{
  "query": {
    "span_multi" : {
      "match" : {
        "prefix" : {"parsedtext" : "joe"}
      }
    }
  }
}
```

Another query to demostate the span_not and span_near:

```json
GET test-index/test-type/_search
{
  "query": {
    "span_not": {
      "include": {
        "span_term": {
          "parsedtext": "nice"
        }
      },
      "exclude": {
        "span_near": {
          "clauses": [
            {
              "span_term": {
                "parsedtext": "not"
              }
            },
            {
              "span_term": {
                "parsedtext": "nice"
              }
            }
          ],
          "slop": 0,
          "in_order": true
        }
      }
    }
  }
}
```

## Match Query

```json
GET test-index/test-type/_search
{
  "query": {
    "match": {
      "parsedtext": {
        "query": "nice guy",
        "operator": "and"
      }
    }
  }
}
```



<!--stackedit_data:
eyJoaXN0b3J5IjpbNzA5NDUxNDg3LDIwMzE4MTY3MDIsNTYwND
E1NjkwLDE5NjEyNzY4MjEsLTEyNTM0NzM4MDUsLTczNTMzMDc4
Nyw1NDc1ODUxNjEsLTE5NDQ4NDM3OTYsMjA5MzQzMDExMSwyMD
M2OTE4MDgzLDQ0Njk5NTY4MSw3MDEwMzcxMDQsODc2MjE1MzE5
LC01MTU1NTE2NTMsLTk2NzcyODgxMiwxMTM3MjczODIyLDEyMT
E5NjMwMzksOTg1OTE5MzY1LDE0MTE4NjY3M119
-->