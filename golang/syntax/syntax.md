# Go Syntax

## 赋值

```go
// 只能用在函数内部，不能用于包变量
s := ""
// 依赖于类型的默认初始化零值机制
var s string
// 用于同时声明多个变量
var s = ""
// 同时声明多个不同类型的变量
var s string = ""
```

实践中一般使用前两个，初始值重要的话显式指定变量类型，否则隐式初始化。  

## slice

```go
s[i] // 访问单个元素
s[m:n] // 获取子序列，是原切片的引用，即修改子序列的值，会改变原切片的值。左闭右开
len(s) // 序列的元素数
```

## for

```go
// 同php的for
for initialization; condition; post {

}

// 同while
for condition {

}

// 死循环
for {

}

// 类似php的foreach
for key, value := range os.Args[1:] {
  
}
```

## make

`make`方法用来为`slice`，`map`，`channel`分配内存，并返回初始值。如下：  

```go
make(map[string]int) // map, chan
make([]int, 2)       // len=2的slice
make([]int, 2, 4)    // len=2，cap=4的slice
```

当我们给`slice`分配内存时，应当尽量预估其可能的最大容量，从而避免在其二次分配内存时带来的系统开销。

## map

```go
// var map1 map[keytype]valuetype
var map1 map[int]string
// 初始化
map1 := make(map[int]string)
```

声明时不用知道map的长度，map可动态增长。  
未初始化的map值为`nil`。  
keytype支持`int`，`string`，`float`。  

## append

`append`方法会向`slice`（切片）末尾追加数据，并返回新的`slice`  
`append`会只能的对底层数组进行容量的扩充，开始是2倍扩容，随着扩容次数的增长，扩容量会有所变化。  

例：

```go
s := make([]int, 5, 10)
  c := cap(s)
  fmt.Println(c)
  for index := 0; index < 60; index++ {
    s = append(s, index)
    if n := cap(s); n > c {
      fmt.Printf("%d -> %d\n", c, n)
      c = n
    }
  }
```

## copy

`copy`方法接收两个切片参数，必须为同一个类型，将第二个参数的元素值按索引复制到第一个切片中。  
`copy`并不会创建新的内存空间。有其原有的切片长度决定。  

```go
a := []int{0, 9, 8, 7, 6, 5, 4, 3, 2, 1}
b := []int{1, 2, 3, 4, 5}
copy(a, b) // a会变为 [1 2 3 4 5]
// copy(b, a) // b会变为[0 9 8 7 6]
```
