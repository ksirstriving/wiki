# Go常用类包之io, io/ioutil

[TOC]

## io

## io/ioutil

> ioutil包封装了一些常用的I/O方法。  

### NopCloser

```go
func NopCloser(r io.Reader) io.ReadCloser
```

### ReadAll

```go
func ReadAll(r io.Reader) ([]byte, error)
```

从r中读取数据直到遇到`EOF`或`error`，返回读取的数据和错误。

参考：

```go
reader := strings.NewReader("hello world")
data, _ := ioutil.ReadAll(reader)
fmt.Println(string(data))
```

### ReadFile

```go
func ReadFile(filename string) ([]byte, error)
```

从指定的文件中读取并返回文件全部内容

参考：

```go
content, err := ioutil.ReadFile(filename)
fmt.Println(string(content))
```

### WriteFile

```go
func WriteFile(filename string, data []byte, perm os.FileMode) error
```

向指定的文件写入数据，如果文件不存在，按`perm`创建指定权限的文件；文件存在则覆盖原内容。  

参考：  

```go
str := "hello world"
ioutil.WriteFile("a.txt", []byte(str), 0666)
```

### ReadDir

```go
func ReadDir(dirname string) ([]os.FileInfo, error)
```

返回`dirname`指定目录的目录信息（有序列表）

参考：

```go
// 递归输出目录
func getDir(dir string, level int) {
  fileInfo, _ := ioutil.ReadDir(dir)
  if len(fileInfo) <= 0 {
    return
  }
  sub := strings.Repeat("  ", level)
  for _, file := range fileInfo {
    fmt.Printf("%s%s\n", sub, file.Name())
    if file.IsDir() {
      getDir(dir+"/"+file.Name(), level+1)
    }
  }
}
```

### TempDir

```go
func TempDir(dir, prefix string) (name string, err error)
```

创建临时目录，`prefix`为临时目录前缀，如果`dir`为空字符串，则使用默认路径。临时目录使用完毕需手动删除  

参考：

```go
tempName, _ := ioutil.TempDir("/tmp", "abc")
```

### TempFile

```go
func TempFile(dir, prefix string) (f *os.File, err error)
```

在`dir`目录下创建一个`prefix`为前缀的临时文件，返回`os.File`指针。临时文件需手动删除。  

参考：

```go
file, err := ioutil.TempFile("./", "tmp_")
```
