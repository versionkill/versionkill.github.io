---
layout: post
title: go 引号的使用
tags: go
comments: true
---

### golang中的双引号`"`、单引号`'`、反引号`` ` ``

#### 1.首先单引号的用法较为简单，主要是针对单个字符来用，直接print的话会输出对应ascall码

```go
a := '好'
fmt.Println(a)
```
输出：
```
22909
```

#### 2.双引号和反引号都可以用来表示字符串,不同之处在于反引号内的字符串转义字符不会被转义，而双引号内的字符串转义字符会被转义

举个栗子：
```
`字\t符\t串\n`
"字\t符\t串\n"
```

结果：

```
字\t符\t串\n
字    符    串
```

------------------------------------------------------------

#### 3.实际开发中，会需要在双引号、反引号括起来的字符串之间转换

```go    
func Unquote(s string) (t string, err error)
```

 Unquote 将“带引号的字符串” s 转换为常规的字符串（不带引号和转义字符）
 s 可以是“单引号”、“双引号”或“反引号”引起来的字符串（包括引号本身）
 如果 s 是单引号引起来的字符串，则返回该该字符串代表的字符

```go
func main() {
	sr, err := strconv.Unquote(`"\"大\t家\t好！\""`)
	fmt.Println(sr, err)
	sr, err = strconv.Unquote(`'大家好！'`)
	fmt.Println(sr, err)
	sr, err = strconv.Unquote(`'好'`)
	fmt.Println(sr, err)
	sr, err = strconv.Unquote("`大\\t家\\t好！`")
	fmt.Println(sr, err)
}
```
结果：

```
"大	家	好！" <nil>
 invalid syntax
好 <nil>
大\t家\t好！ <nil>
```

------------------------------------------------------------

```go
func Quote(s string) string
```

 Quote 将字符串 s 转换为“双引号”引起来的字符串
 其中的特殊字符将被转换为“转义字符”
 “不可显示的字符”将被转换为“转义字符”

```go
func main() {
	fmt.Println(strconv.Quote(`C:\Windows`))	
}
```

结果：

```
"C:\\Windows"
```
  
------------------------------------------------------------

#### 4.此外，使用占位符 %v %q %#v %#q可以实现一些简单转换
