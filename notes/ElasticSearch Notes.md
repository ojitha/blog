
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
  }
}
```
The result will give scrolling id.

> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbNzAxMDM3MTA0LDg3NjIxNTMxOSwtNTE1NT
UxNjUzLC05Njc3Mjg4MTIsMTEzNzI3MzgyMiwxMjExOTYzMDM5
LDk4NTkxOTM2NSwxNDExODY2NzNdfQ==
-->