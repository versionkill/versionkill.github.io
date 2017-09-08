---
layout: post
title: markdown 笔记
tags: tools
comments: true
---

# 一、分割线

使用`***`显示分隔线，必须三个以上，允许之间存在空格

```
* * *

***

---
```

效果如下：

---

* * *

***



# 二、标题

### 1.使用`#`来显示标题，几级标题就加几个`#`

```
# This is an H1
```

效果如下：

# This is an H1

```
## This is an H2
```

效果如下：

## This is an H2


### 2.使用在标题下方加`===`或`---`方式，这边并不限制使用几个这样的符合，一个也行

```
This is an H1
=======
```

效果如下：

This is an H1
=======

```
This is an H2
-------
```

效果如下：

This is an H2
-------

***

# 三、强调

```
*asdasdasdasd* 

_asdasdasdasdasdasd_

**asdasdasdasd**

__asdasdassdfsdfs__
```

效果如下：

*asdasdasdasd* 

_asdasdasdasdasdasd_


**asdasdasdasd**

__asdasdassdfsdfs__

如果是要插入特殊字符，需要加转义字符

\*asdasdasdasd\* 

***

# 四、代码

### 1.代码段

`` ```go ``

``package main``

``import("fmt")``


``func main(){``

``  fmt.Println("Hello World")``

``}``

`` ``` ``

	```go
	package main
	import(
		"fmt"
	)
	func main(){
		fmt.Println("Hello World")
	}
	```

效果如下：

```go
package main
import(
	"fmt"
)
func main(){
	fmt.Println("Hello World")
}
```

### 2.行内代码

1. 用两个`` ` `` 显示行内要显示的代码

``asdasda`<1246609979@qq.com>`asdasdasdasda``

效果如下：

asdasda`<1246609979@qq.com>`asdasdasdasda


2. 用多个`` ` ``加空格来显示出`` ` ``
```
`` There is a literal backtick (`) here.``
```

效果如下：

``There is a literal backtick (`) here.``

3.
```
A backtick-delimited: `` `foo` ``
```

效果如下：

A backtick-delimited : `` `foo` ``


***

# 五、图片

### 1.使用!\[ALT TXT\]\(PATH\) 显示图片

```
![Alt text](/path/to/img.jpg)

```

效果如下：

![Alt text](/path/to/img.jpg)

```
![Alt text2](D:/john/1452419577376.jpg)

```

效果如下：

![Alt text2](https://versionkill.github.io/images/avatar.jpg)


### 2.使用id方式,并可以实现鼠标位于图片上显示相应文字

```
![Alt text3][id]

[id]: D:/john/1452419577376.jpg "JOHN TEST"

```

效果如下：

![Alt text3][id]

[id]: D:/john/1452419577376.jpg "JOHN TEST"

***

# 六、列表

### 1.用\*来显示列表，行内无所谓对齐，\*与文本留空格

```
*   Lorem ipsum dolor sit amet, consectetuer adipiscing elit.
    Aliquam hendrerit mi posuere lectus. Vestibulum enim wisi,
viverra nec, fringilla in, laoreet vitae, risus.
*   Donec sit amet nisl. Aliquam semper ipsum sit amet velit.
    Suspendisse id sem consectetuer libero luctus adipiscing.
```

效果如下：

*   Lorem ipsum dolor sit amet, consectetuer adipiscing elit.
    Aliquam hendrerit mi posuere lectus. Vestibulum enim wisi,
    viverra nec, fringilla in, laoreet vitae, risus.
*   Donec sit amet nisl. Aliquam semper ipsum sit amet velit.
    Suspendisse id sem consectetuer libero luctus adipiscing.

### 2.列表中如果有引用\>，那就必须对齐

```
*   This is a list item with two paragraphs.

    >This is the second paragraph in the list item. You're
    >only required to indent the first line. Lorem ipsum dolor
    >sit amet, consectetuer adipiscing elit.
*   Another item in the same list.
```

效果如下：

*   This is a list item with two paragraphs.

    >This is the second paragraph in the list item. You're
    >only required to indent the first line. Lorem ipsum dolor
    >sit amet, consectetuer adipiscing elit.
*   Another item in the same list.
  
### 3.列表以数字开头的标识，有固定的位置，并且有最大长度，如果超出相应长度就会显示到屏幕外

123223. What a great season.


1232. What a great season.

### 4.列表以数字开头的标识，如果只是想显示对应的字符串唯一，那么需要对`.`做转义

123\. What a great season.


***

# 七、表格

### 1.因为markdown支持html语法，所以可以直接使用html方式添加表格，但是发现有的地方确实是无法起到效果，不知为何

```html
<table>
   <tr>
      <td>John</td>
      <td>Smith</td>
      <td>123 Main St.</td>
      <td>Springfield</td>
   </tr>
   <tr>
      <td>Mary</td>
      <td>Jones</td>
      <td>456 Pine St.</td>
      <td>Dover</td>
   </tr>
   <tr>
      <td>Jim</td>
      <td>Baker</td>
      <td>789 Park Ave.</td>
      <td>Lincoln</td>
   </tr>
</table>
```

效果如下：

<table>
   <tr>
      <td>John</td>
      <td>Smith</td>
      <td>123 Main St.</td>
      <td>Springfield</td>
   </tr>
   <tr>
      <td>Mary</td>
      <td>Jones</td>
      <td>456 Pine St.</td>
      <td>Dover</td>
   </tr>
   <tr>
      <td>Jim</td>
      <td>Baker</td>
      <td>789 Park Ave.</td>
      <td>Lincoln</td>
   </tr>
</table>

### 2. 使用markdown语法创建table

head 下方的`-`并不限制数量，为了好看，尽量对齐便可，`:`影响的是对齐方式，默认head是居中，body部分居左，两边都加则让head和内容都居中显示

```
| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |
```

效果如下：

| Tables        | Are           | Cool  |
| ------------- |:-------------:| -----:|
| col 3 is      | right-aligned | $1600 |
| col 2 is      | centered      |   $12 |
| zebra stripes | are neat      |    $1 |

# 八、链接

### 1.使用`[标识][]`配合`[标识]: 链接` 方式来显示连接,网址定义只有在产生链接的时候用到，并不会直接出现在文件之中。

```
Visit [Google website][] for more information.

[Google website]: http://google.com/
```

效果如下：

Visit [Google website][] for more information.

[Google website]: http://google.com/


### 2.使用id 方式

```
This is [百度][baidu] reference-style link.

[baidu]: http://www.baidu.com/  "http://www.baidu.com"
```

效果如下：

This is [百度][baidu] reference-style link.

[baidu]: http://www.baidu.com/  "http://www.baidu.com"


### 3.使用`<>`来显示链接

```go
my email: <390801797@qq.com>
```

效果如下：

my email: <390801797@qq.com>

### 4.使用`[标识](url "description")`

1.带描述的隐藏原URL地址的链接方式
```
This is [an example](http://www.baidu.com/ "Title") inline Link.
```

效果如下：

This is [an example](http://www.baidu.com/ "Title") inline Link.

2.可缺省desc

```
This is [an example](http://www.baidu.com/) inline Link.

```

效果如下：

This is [an example](http://www.baidu.com/) inline Link.

3.另外这种方式还可以用来链接到同样主机的资源，你可以使用相对路径

```
See my [About](C:/Users/johnji/) page for details.
```

See my [About](C:/Users/johnji/) page for details.
