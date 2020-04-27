# Go常用类包只net/http

`http`包提供了HTTP客户端和服务端的实现。  
Get, Head, Post, PostForm函数发出HTTP/HTTPS请求。  

```go
resp, err := http.Get("http://example.com")
resp, err := http.Post("http://example.com/upload", "image/jpeg", &buf)
resp, err := http.PostForm("http://example.com/postform", url.Values{"key":{"value"}, "id":{1}})
```

在获取回复后必须关闭回复主体。  

```go
resp, err := http.Get("http://example.com")
if err != nil {
  
}
```
