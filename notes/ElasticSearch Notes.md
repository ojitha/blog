
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

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbNTYwNDE1NjkwLDE5NjEyNzY4MjEsLTEyNT
M0NzM4MDUsLTczNTMzMDc4Nyw1NDc1ODUxNjEsLTE5NDQ4NDM3
OTYsMjA5MzQzMDExMSwyMDM2OTE4MDgzLDQ0Njk5NTY4MSw3MD
EwMzcxMDQsODc2MjE1MzE5LC01MTU1NTE2NTMsLTk2NzcyODgx
MiwxMTM3MjczODIyLDEyMTE5NjMwMzksOTg1OTE5MzY1LDE0MT
E4NjY3M119
-->