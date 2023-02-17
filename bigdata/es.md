## CRUD
### 新增index
1. http操作
```
put http://ip:port/index_name
eg: http://127.0.0.1:9200/shopping
```

1. kinaba操作
   
```
<!-- PUT people/_create/1
{
  "user": "Clearlove",
  "desc": "777777777"
} -->
```

### 查看所有index
1. http
```
get http://ip:port/_cat/indices
eg:http://127.0.0.1:9200/_cat/indices
```
2. kinaba
```

```

### 查看单个index
1. http
```
get http://ip:port/_cat/indices/index_name
http://127.0.0.1:9200/_cat/indices/shopping
```

2. kinaba
```
```

### 新增文档
1. http
2. kinaba
```
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


```