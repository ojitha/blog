
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

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTgxMTM4OTY1LC0xMjUzNDczODA1LC03Mz
UzMzA3ODcsNTQ3NTg1MTYxLC0xOTQ0ODQzNzk2LDIwOTM0MzAx
MTEsMjAzNjkxODA4Myw0NDY5OTU2ODEsNzAxMDM3MTA0LDg3Nj
IxNTMxOSwtNTE1NTUxNjUzLC05Njc3Mjg4MTIsMTEzNzI3Mzgy
MiwxMjExOTYzMDM5LDk4NTkxOTM2NSwxNDExODY2NzNdfQ==
-->