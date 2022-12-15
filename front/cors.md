## CORS
跨域资源访问。浏览器将请求分为两类
- 简单请求
- 非简单请求

### 简单请求
对于简单请求，直接发送请求，并添加一个origin头
```http
GET /cors HTTP/1.1
Origin: http://m.xin.com
Host: api.alice.com
Accept-Language: en-US
Connection: keep-alive
User-Agent: Mozilla/5.0...
```

满足以下条件就是简单请求
1. 如下请求方法
- HEAD
- GET
- POST
2. 只有如下请求头或者部分
- Accept
- Accept-Language
- Content-Language
- Last-Event-ID
- Content-Type（application/x-www-form-urlencoded、multipart/form-data、text/plain)

服务器回复中的Header包含了是否接受浏览器的请求
1. **Access-Control-Allow-Origin: a.b.c**
    表示接受来自a.b.c的请求
2. **Access-Control-Allow-Credentials: true**
    表示是否允许发送cookies
3. **Access-Control-Expose-Headers: FooBar**
    指定浏览器可以拿到的额外的头部信息，因为浏览器只能拿到Cache-Control、Content-Language、Content-Type、Expires、Last-Modified、Pragma这几个Header之一
4. **Content-Type: text/html; charset=utf-8**
    回复的body的格式

### 非简单请求
不属于上述两种情况下。例如：请求的Content-type为json格式，或者请求方法是delete、put
非简单请求的CORS请求，会在正式通信之前，增加一次HTTP查询请求(preflight)，
浏览器先询问服务器，当前网页所在的域名是否在服务器的许可名单之中，以及可以使用哪些HTTP动词和头信息字段。只有得到肯定答复，浏览器才会发出正式的XMLHttpRequest请求，否则就报错。
例如以下的请求代码
```javascript
var url = 'http://a.b.c';
var xhr = new XMLHttpRequest();
xhr.open('PUT', url, true);
xhr.setRequestHeader('X-Custom-Header', 'value');
xhr.send();
```
上述的请求方式是put，请还有自定义的请求头，那么浏览器会先发送查询请求.以下是请求格式
```http
OPTIONS /cors HTTP/1.1
Origin: http://a.b.c
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: X-Custom-Header
Host: aa.bb.cc
Accept-Language: en-US
Connection: keep-alive
User-Agent: Mozilla/5.0...
```
请求方式是OPTIONS。
- Origin 
指出浏览器的域
- Access-Control-Request-Method
指出浏览器将会使用的请求方法
- Access-Control-Request-Headers
指出浏览器附带的额外的请求头

#### 服务器的响应
服务器检查对应的头信息后，确定回复信息
```http
HTTP/1.1 200 OK
Date: Mon, 01 Dec 2008 01:15:39 GMT
Server: Apache/2.0.61 (Unix)
Access-Control-Allow-Origin: http://m.a.com
Access-Control-Allow-Methods: GET, POST, PUT
Access-Control-Allow-Headers: X-Custom-Header
Content-Type: text/html; charset=utf-8
Content-Encoding: gzip
Content-Length: 0
Keep-Alive: timeout=2, max=100
Connection: Keep-Alive
Content-Type: text/plain
```
- Access-Control-Allow-Origin
指定可以访问服务器的浏览器的域，*表示不限定
- Access-Control-Allow-Methods: GET, POST, PUT
指出可以访问服务器的浏览器可以使用的请求方法
- Access-Control-Allow-Headers: X-Custom-Header
指出浏览器可以附带的额外的请求头
- Content-Type: text/html; charset=utf-8

如果服务器不同意，则会返回一个正常的http响应，但是因为没有这些关键的头信息，浏览器任务服务器拒绝资源访问。