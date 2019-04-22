
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
After search
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
> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE5NDQ4NDM3OTYsMjA5MzQzMDExMSwyMD
M2OTE4MDgzLDQ0Njk5NTY4MSw3MDEwMzcxMDQsODc2MjE1MzE5
LC01MTU1NTE2NTMsLTk2NzcyODgxMiwxMTM3MjczODIyLDEyMT
E5NjMwMzksOTg1OTE5MzY1LDE0MTE4NjY3M119
-->