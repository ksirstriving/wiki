# Go常用类包之bufio

[TOC]

## Scanner类型

### NewScanner()

```go
func NewScanner(r io.Reader) *Scanner
```

创建并返回从r读取数据的Scanner，默认分隔函数是`ScanLines`

### Split()

```go
func (s *Scanner) Split(split SplitFunc)
```

用来设置该Scanner的分割函数。必须在`Scan`方法前被调用

### Scan()

```go
func (s *Scanner) Scan
```

`Scan`方法会扫描当前位置的内容，并将指针后移，直到扫描抵达输入流结尾（`EOF`）或是遇到错误而终止。此时方法会返回`false`，`Err`方法会记录错误信息。如果是到达结尾，`Err`会返回`nil`。  
在使用`os.Stdin`输入时，使用`control+d`键入`EOF`。

### Bytes()

```go
func (s *Scanner) Bytes() []byte
```

以`byte`类型返回最近一次`Scan`方法扫描的内容。

### Text()

```go
func (s *Scanner) Text() string
```

以`string`类型返回最近一次`Scan`方法扫描的内容。

### Err()

```go
func (s *Scanner) Err() error
```

返回`Scanner`遇到的第一个非`EOF`的错误

---
