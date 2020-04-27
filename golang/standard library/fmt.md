# Go常用类包之fmt

[TOC]

> 实现I/O格式化，类似于C的printf和scanf。

## 1. 占位符

### 1.1 通用

| verbs | intro                                                     |
|-------|-----------------------------------------------------------|
| %v    | 默认方式输出，当打印struct类型时，使用`(%+v)`可以显示字段名 |
| %#v   | 打印变量值，以go语法方式呈现                               |
| %T    | 打印变量的类型                                            |
| %%    | 打印'%'                                                   |

示例：

```go
type T struct {
  a int
  b string
  c []int
}

a := new(T)
fmt.Printf("%v") // &{0  []}
fmt.Printf("%+v") // &{a:0 b: c:[]}
fmt.Printf("%#v") // &main.a{a:0, b:"", c:[]int(nil)}
fmt.Printf("%T", a) // main.a
fmt.Printf("%%") // %
```

### 1.2 布尔值（Boolean）

| verbs | intro               |
|-------|---------------------|
| %t    | 打印`true`或`false` |

示例：

```go
fmt.Printf("%t", true) // true
fmt.Printf("%t", false) // false
fmt.Printf("%t", 1) // !t(int=1) 如果变量不是布尔值，会输出对应值
```

### 1.3 整型值（Integer）

| verbs | intro             |
|-------|-------------------|
| %b    | 二进制            |
| %d    | 十进制            |
| %o    | 八进制            |
| %x    | 十六进制，字母小写 |
| %X    | 十六进制，字母大写 |
| %c    | 将Unicode码转换为对应字符 |
| %q    | 将Unicode码转换为对应字符，并用单引号围绕 |
| %U    | Unicode格式输出 |

示例：

```go
fmt.Printf("%b", 30) // 11110
fmt.Printf("%d", 30) // 30
fmt.Printf("%o", 30) // 36
fmt.Printf("%x", 30) // 1e
fmt.Printf("%X", 30) // 1E
fmt.Printf("%c", 0x4E2D) // 中
fmt.Printf("%q", 0x4E2D) // '中'
fmt.Printf("%U", 100) // U+0064
```

### 1.4 浮点数（Floating）

| verbs | intro                                       |
|-------|---------------------------------------------|
| %e    | 科学计数法（e）                               |
| %E    | 科学计数法（E）                               |
| %f    | 带小数点的非指数形式                        |
| %g    | 根据情况选择 %e 或 %f，更紧凑的输出（末尾无0） |
| %G    | 根据情况选择 %E 或 %f，更紧凑的输出（末尾无0） |

示例：

```go
fmt.Printf("%e", 10.02) // 1.002000e+01
fmt.Printf("%E", 10.02) // 1.002000E+01
fmt.Printf("%.3f", 1.02) // 1.020
fmt.Printf("%g", 1.02) // 1.02
fmt.Printf("%G", 1000000.0) // 1E+06
```

> 宽度和精度：  
> 宽度默认为值输出的最小字符数，当要格式化展示时，不足宽度的值会用空格补充  
> 精度为小数点后的保留位数

### 1.5 字符串（String）

| verbs | intro                                  |
|-------|----------------------------------------|
| %s    | 输出字符串，变量类型可以是string，[]byte |
| %q    | 输出双引号包括的字符串                 |
| %x    | 转为十六进制，小写                      |
| %X    | 转为十六进制，大写                      |

示例：

```go
fmt.Printf("%s", "hello world") // hello world
fmt.Printf("%q", "hello world") // hello world
fmt.Printf("%x", "hello world") // 68656c6c6f20776f726c64
fmt.Printf("%X", "hello world") // 68656C6C6F20776F726C64
```

### 1.6 切片（Slice）

| verbs | intro                                    |
|-------|------------------------------------------|
| %p    | 输出切片第0个元素的十六进制地址，以0x开头 |

示例：

```go
fmt.Printf("%p", slice) // 0xc000016060
fmt.Printf("%v", &slice[0]) // 0xc000016060
```

### 1.7 指针（Pointer）

| verbs | intro                                    |
|-------|------------------------------------------|
| %p    | 输出切片第0个元素的十六进制地址，以0x开头 |

> %b, %d, %o %x, %X 同样可以用来输出指针类型。

---

## 2. 方法

### 2.1 Scan系列

`fmt.Scan`

```go
fmt.Scan(&name, &age, &married)
```

从标准输入中扫描文本，空格用于分隔值，并赋值在对应的变量上。  
当变量数量不匹配或是格式错误，会直接退出并返回错误。  

`fmt.Scanf`

```go
fmt.Scanln("1:%s 2:%d 3:%t", &name, &age, &married)
```

同`fmt.Scan`类似，需要按定义的格式输入，否则会异常退出。  

`fmt.Scanln`

```go
fmt.Scanln(&name, &age, &married)
```

同`fmt.Scan`类似，当输入换行符后，便会结束。  

---

### 2.2 Fscan系列

`fmt.Fscan`  
`fmt.Fscanln`  
`fmt.Fscanf`  

```go
fmt.Fscanf(file, "%s", &a)
```

函数功能分别对应`fmt.scan`, `fmt.scanln`, `fmt.scanf`，只是输入从标准输入变为了从文件（`io.Reader`）中读取。  

### 2.3 Sscan系列

`fmt.Sscan`  
`fmt.Sscanln`  
`fmt.Sscanf`  

```go
fmt.Fscanf(file, "%s", &a)
```

函数功能分别对应`fmt.scan`, `fmt.scanln`, `fmt.scanf`，只是输入从标准输入变为了从指定字符串中读取。  

