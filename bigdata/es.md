## CRUD
### 新增index
1. http操作
```
- http
put http://ip:port/index_name
eg: http://127.0.0.1:9200/shopping


- curl
curl -Xput "http://localhost:9200/h5_log?pretty"

curl -Xput "http://elasticsearch:9200/orders" -H 'Content-Type: application/json' -d'
{
  "settings":{
    "index": {
      "number_of_shards":1,
      "number_of_replicas":2
    }
  }
}'

- kibana
put /h5_log?pretty

put orders
{
  "settings":{
    "index": {
      "number_of_shards":1,
      "number_of_replicas":2
    }
  }
}





```

### 查看所有index
1. http
```

get http://ip:port/_cat/indices?v
eg:http://127.0.0.1:9200/_cat/indices?v

- kinaba

get /_cat/indices?v

- curl
curl -Xget "http://localhost:9200/_cat/indices?v"
```

### 查看单个index
1. http
```
get http://ip:port/_cat/indices/index_name
http://127.0.0.1:9200/_cat/indices/

- curl
curl -Xget "http://localhost:9200/_cat/indices/h5_log?v"
```

### 新增文档
```
- kibana
PUT index_name/_create/docs_id
eg:
PUT users/_create/1
{
  "user": "ll",
  "desc": "0119"
}

POST index_name/_create/docs_id
eg:
POST users/_create/2
{
  "user": "Uzi",
  "desc": "forever god"
}
- curl
curl -Xput "http://elasticsearch:9200/h5_log/_create/1" -H 'Content-Type: application/json' -d'
{
  "user_id":"liulu17",
  "spm":"10.101.102.103"
}'
```

### 查看所有文档
```
- kibana
GET /h5_log/_search?q=*

- curl
curl -XGET "http://localhost:9200/h5_log/_search?q=*"
```