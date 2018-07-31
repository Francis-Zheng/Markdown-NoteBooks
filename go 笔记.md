<!-- TOC -->

- [1. Go 参考资料](#1-go-参考资料)
- [2. Go常用命令](#2-go常用命令)
    - [2.1. go vet](#21-go-vet)
    - [2.2. go test](#22-go-test)
- [3. Go 函数与方法](#3-go-函数与方法)
    - [3.1. 函数](#31-函数)
    - [3.2. 方法](#32-方法)
    - [3.3. 值接收者和指针接收者](#33-值接收者和指针接收者)
        - [3.3.1. 值接收者](#331-值接收者)
        - [3.3.2. 指针接收者](#332-指针接收者)
- [4. Go 语言-函数作为值](#4-go-语言-函数作为值)
- [5. Go 语言-函数闭包](#5-go-语言-函数闭包)
- [6. Go 语言变量作用域](#6-go-语言变量作用域)
- [7. Go 语言指针数组](#7-go-语言指针数组)
- [8. Go 语言切片(Slice)](#8-go-语言切片slice)
    - [8.1. 定义切片](#81-定义切片)
    - [8.2. 切片初始化](#82-切片初始化)
    - [8.3. len() 和 cap() 函数](#83-len-和-cap-函数)
    - [8.4. 空(nil)切片](#84-空nil切片)
    - [8.5. 切片截取](#85-切片截取)
    - [8.6. append() 和 copy() 函数](#86-append-和-copy-函数)
- [9. Go 语言-范围(Range)](#9-go-语言-范围range)
- [10. Go 语言-Map(集合)](#10-go-语言-map集合)
    - [10.1. 定义 Map](#101-定义-map)
    - [10.2. delete() 函数](#102-delete-函数)
- [11. Go interface](#11-go-interface)
    - [11.1. interface定义与实现的模板 实例1](#111-interface定义与实现的模板-实例1)
    - [11.2. interface使用 实例2](#112-interface使用-实例2)
    - [11.3. interface实例3](#113-interface实例3)
    - [11.4. Q1：为什么s可以直接调用Put函数](#114-q1为什么s可以直接调用put函数)
    - [11.5. Q2：f函数为什么也能Get()](#115-q2f函数为什么也能get)
    - [11.6. Q3：f2的interface{}空接口用途](#116-q3f2的interface空接口用途)
    - [11.7. Q4：f1 f2 为什么要使用指针?](#117-q4f1-f2-为什么要使用指针)
- [12. 理解Go interface的6个关键点](#12-理解go-interface的6个关键点)
    - [12.1. interface 是一种类型](#121-interface-是一种类型)
    - [12.2. interface 变量存储的是实现者的值](#122-interface-变量存储的是实现者的值)
    - [12.3. 如何判断 interface 变量存储的是哪种类型](#123-如何判断-interface-变量存储的是哪种类型)
    - [12.4. 实现接口的方法集：指针接收者、值接收者](#124-实现接口的方法集指针接收者值接收者)
        - [12.4.1. 值接收者](#1241-值接收者)
        - [12.4.2. 指针接收者](#1242-指针接收者)
        - [12.4.3. 小结：](#1243-小结)
        - [12.4.4. 再举个例子](#1244-再举个例子)
    - [12.5. 空的 interface](#125-空的-interface)
- [13. Go 嵌入类型](#13-go-嵌入类型)
    - [13.1. 用于接口类型](#131-用于接口类型)
    - [13.2. 用于结构体类型](#132-用于结构体类型)
    - [13.3. 嵌入类型的使用](#133-嵌入类型的使用)
    - [13.4. 外部类型覆盖内部类型](#134-外部类型覆盖内部类型)
- [14. Go 反射](#14-go-反射)
    - [14.1. TypeOf和ValueOf](#141-typeof和valueof)
    - [14.2. 获取类型底层类型](#142-获取类型底层类型)
    - [14.3. 遍历字段和方法](#143-遍历字段和方法)
    - [14.4. 修改字段的值](#144-修改字段的值)
    - [14.5. 动态调用方法](#145-动态调用方法)
- [15. Go Struct Tag](#15-go-struct-tag)
    - [15.1. JSON字符串对象转换](#151-json字符串对象转换)
    - [反射获取字段Tag](#反射获取字段tag)
    - [字段Tag的键值对](#字段tag的键值对)

<!-- /TOC -->

# 1. Go 参考资料
1. 菜鸟教程 http://www.runoob.com/go/go-method.html
2. 《Go语言实战》读书笔记：http://www.flysnow.org/categories/Golang/page/4/

# 2. Go常用命令

## 2.1. go vet
这个命令不会帮助开发人员写代码，但是它也很有用，因为它会帮助我们检查我们代码中常见的错误。

Printf这类的函数调用时，类型匹配了错误的参数。
定义常用的方法时，方法签名错误。
错误的结构标签。
没有指定字段名的结构字面量。

```go
package main
import (
	"fmt"
)
func main() {
	fmt.Printf(" 哈哈",3.14)
}
```
这个例子是一个明显错误的例子，新手经常会犯，这里我们忘记输入了格式化的指令符，这种编辑器是检查不出来的，但是如果我们使用go vet就可以帮我们检查出这类常见的小错误。

其使用方式和go fmt一样，也是接受一个包名作为参数。
```go
usage: go vet [-n] [-x] [build flags] [packages]
```

```go
➜  hello go vet
main.go:8: no formatting directive in Printf call
```
## 2.2. go test
该命令用于Go的单元测试，它也是接受一个包名作为参数，如果没有指定，使用当前目录。
`go test`运行的单元测试必须符合go的测试要求:

1. 写有单元测试的文件名，必须以`_test.go`结尾。
2. 测试文件要包含若干个测试函数。
3. 这些测试函数要以`Test`为前缀，还要接收一个`*testing.T`类型的参数。

```go
package main
import "testing"
func TestAdd(t *testing.T) {
	if Add(1,2) == 3 {
		t.Log("1+2=3")
	}
	if Add(1,1) == 3 {
		t.Error("1+1=3")
	}
}
```

上面的代码是一个单元测试，保存在`main_test.go`文件中，对`main`包里的`Add(a,b int)`函数进行单元测试。
如果要运行这个单元测试，在该文件目录下，执行`go test` 即可。

```go
➜  hello go test
PASS
ok  	flysnow.org/hello	0.006s
```

以上是打印输出，测试通过。更多关于`go test`命令的使用，请通过如下命令查看。


# 3. Go 函数与方法

## 3.1. 函数
函数和方法，虽然概念不同，但是定义非常相似。函数的定义声明没有接收者，所以我们直接在go文件里，go包之下定义声明即可。
```go
func main() {
	sum := add(1, 2)
	fmt.Println(sum)
}
func add(a, b int) int {
	return a + b
}
```
例子中，我们定义了add就是一个函数，它的函数签名是`func add(a, b int) int`,没有接收者，直接定义在go的一个包之下，可以直接调用，比如例子中的`main`函数调用了`add`函数。

例子中的这个函数名称是小写开头的`add`，所以它的作用域只属于所声明的包内使用，不能被其他包使用，如果我们把函数名以**大写字母开头，该函数的作用域就大了，可以被其他包调用**。这也是Go语言中大小写的用处，比如Java中，就有专门的关键字来声明作用域private、protect、public等。

```go
/*
 提供的常用库，有一些常用的方法，方便使用
*/
package lib
// 一个加法实现
// 返回a+b的值
func Add(a, b int) int {
	return a + b
}
```
如上例子中定义的`Add`方法就可以被其他包调用。

## 3.2. 方法
方法的声明和函数类似，他们的区别是：方法在定义的时候，会在`func`和`方法名`之间增加一个参数，这个参数就是`接收者`，这样我们定义的这个方法就和接收者绑定在了一起，称之为这个接收者的方法。

```go
type person struct {
	name string
}
func (p person) String() string{
	return "the person name is "+p.name
}
```

留意例子中，`func`和`方法名`之间增加的参数`(p person)`,这个就是接收者。现在我们说，类型`person`有了一个`String`方法，现在我们看下如何使用它。

```go
func main() {
	p:=person{name:"张三"}
	fmt.Println(p.String())
}
```

调用的方法非常简单，使用类型的变量进行调用即可，类型变量和方法之前是一个`.`操作符，表示要调用这个类型变量的某个方法的意思。

## 3.3. 值接收者和指针接收者

Go语言里有两种类型的接收者：**值接收者**和**指针接收者**。我们上面的例子中，就是使用值类型接收者的示例。

### 3.3.1. 值接收者
使用值类型接收者定义的方法，在调用的时候，使用的其实是值接收者的一个副本，所以对该值的任何操作，不会影响原来的类型变量。如下例：

```go
func main() {
	p:=person{name:"张三"}
	p.modify() //值接收者，修改无效
	fmt.Println(p.String())
}
type person struct {
	name string
}
func (p person) String() string{
	return "the person name is "+p.name
}
func (p person) modify(){
	p.name = "李四"
}
```

以上的例子，打印出来的值还是`张三`，对其进行的修改无效。

### 3.3.2. 指针接收者

如果我们使用一个指针作为接收者，那么就会其作用了，因为指针接收者传递的是一个指向原值指针的副本，指针的副本，指向的还是原来类型的值，所以修改时，同时也会影响原来类型变量的值。

```go
func main() {
	p:=person{name:"张三"}
	p.modify() //指针接收者，修改有效
	fmt.Println(p.String())
}
type person struct {
	name string
}
func (p person) String() string{
	return "the person name is "+p.name
}
func (p *person) modify(){
	p.name = "李四"
}
```

在上面的例子中，有没有发现，我们在调用指针接收者方法的时候，使用的也是一个值的变量，并不是一个指针，如果我们使用下面的也是可以的。
```go
p:=person{name:"张三"}
(&p).modify() //指针接收者，修改有效
```

这样也是可以的。如果我们没有这么强制使用指针进行调用，Go的编译器自动会帮我们取指针，以满足接收者的要求。

同样的，如果是一个值接收者的方法，使用指针也是可以调用的，Go编译器自动会解引用，以满足接收者的要求，比如例子中定义的String()方法，也可以这么调用：

```go
p:=person{name:"张三"}
fmt.Println((&p).String())
```

总之，方法的调用，既可以使用值，也可以使用指针，我们不必要严格的遵守这些，Go语言编译器会帮我们进行自动转义的，这大大方便了我们开发者。

不管是使用值接收者，还是指针接收者，一定要搞清楚**类型的本质**：对类型进行操作的时候，是要改变当前值，还是要创建一个新值进行返回？这些就可以决定我们是采用值传递，还是指针传递。


# 4. Go 语言-函数作为值

Go 语言可以很灵活的创建函数，并作为值使用。以下实例中我们在定义的函数中初始化一个变量，该函数仅仅是为了使用内置函数 `math.sqrt()` ，实例为：

```go
package main

import (
   "fmt"
   "math"
)

func main(){
   /* 声明函数变量 */
   getSquareRoot := func(x float64) float64 {
      return math.Sqrt(x)
   }

   /* 使用函数 */
   fmt.Println(getSquareRoot(9))

}
```

以上代码执行结果为：
```go
3
```


# 5. Go 语言-函数闭包

Go 语言支持匿名函数，可作为闭包。匿名函数是一个"内联"语句或表达式。匿名函数的优越性在于可以直接使用函数内的变量，不必申明。

以下实例中，我们创建了函数 `getSequence()` ，返回另外一个函数。该函数的目的是在闭包中递增 i 变量，代码如下：

```go
package main

import "fmt"

func getSequence() func() int {
   i:=0
   return func() int {
      i+=1
     return i  
   }
}

func main(){
   /* nextNumber 为一个函数，函数 i 为 0 */
   nextNumber := getSequence()  

   /* 调用 nextNumber 函数，i 变量自增 1 并返回 */
   fmt.Println(nextNumber())
   fmt.Println(nextNumber())
   fmt.Println(nextNumber())
   
   /* 创建新的函数 nextNumber1，并查看结果 */
   nextNumber1 := getSequence()  
   fmt.Println(nextNumber1())
   fmt.Println(nextNumber1())
}
```

以上代码执行结果为：
```go
1
2
3
1
2
```

# 6. Go 语言变量作用域

- Go 语言程序中全局变量与局部变量名称可以相同，但是函数内的局部变量会被优先考虑。实例如下：

```go
package main

import "fmt"

/* 声明全局变量 */
var g int = 20

func main() {
   /* 声明局部变量 */
   var g int = 10

   fmt.Printf ("结果： g = %d\n",  g)
}
```
以上实例执行输出结果为：

```go
结果： g = 10
```

- 初始化局部和全局变量

**不同类型的局部和全局变量默认值为：**

|数据类型|	初始化默认值|
|:----|:----|
|int|    0|
|float32	|0|
|pointer	|nil|

# 7. Go 语言指针数组

有一种情况，我们可能需要保存数组，这样我们就需要使用到指针。

以下声明了整型指针数组：
```go
var ptr [MAX]*int;
```
ptr 为整型指针数组。因此每个元素都指向了一个值。以下实例的三个整数将存储在指针数组中：
```go
package main

import "fmt"

const MAX int = 3

func main() {
   a := []int{10,100,200}
   var i int
   var ptr [MAX]*int;

   for  i = 0; i < MAX; i++ {
      ptr[i] = &a[i] /* 整数地址赋值给指针数组 */
   }

   for  i = 0; i < MAX; i++ {
      fmt.Printf("a[%d] = %d\n", i,*ptr[i] )
   }
}
```
以上代码执行输出结果为：
```go
a[0] = 10
a[1] = 100
a[2] = 200
```

# 8. Go 语言切片(Slice)

Go 语言切片是对数组的抽象。

Go 数组的长度不可改变，在特定场景中这样的集合就不太适用，Go中提供了一种灵活，功能强悍的`内置类型切片("动态数组")`,与数组相比切片的长度是不固定的，`可以追加元素`，在追加时可能使切片的容量增大。

## 8.1. 定义切片
你可以声明一个未指定大小的数组来定义切片：
```go
var identifier []type
```
切片不需要说明长度。

或使用make()函数来创建切片:

```go
var slice1 []type = make([]type, len)
```

也可以简写为:
```go
slice1 := make([]type, len)
```
也可以指定容量，其中`capacity`为可选参数。
```go
make([]T, length, capacity)
```
这里 `len` 是数组的长度并且也是切片的初始长度。

## 8.2. 切片初始化

1. 直接初始化切片，`[]`表示是切片类型，`{1,2,3}`初始化值依次是`1,2,3`.其`cap`(切片最长可到多少)`=len`(切片当前长度)`=3`
```go
s :=[] int {1,2,3 } 
```

2. 初始化切片`s`,是数组`arr`的引用
```go
s := arr[:] 
```

3. 将`arr`中从下标`startIndex`到`endIndex-1` 下的元素创建为一个新的切片

```go
s := arr[startIndex:endIndex] 
```

4. 缺省`endIndex`时将表示一直到`arr`的最后一个元素
```go
s := arr[startIndex:] 
```

5. 缺省`startIndex`时将表示从`arr`的第一个元素开始
```go
s := arr[:endIndex] 
```
6. 通过切片`s`初始化切片`s1`
```go
s1 := s[startIndex:endIndex] 
```

7. 通过内置函数`make()`初始化切片`s`,`[]int` 标识为其元素类型为`int`的切片
```go
s :=make([]int,len,cap) 
```

## 8.3. len() 和 cap() 函数
切片是可索引的，并且可以由 `len()` 方法获取长度。

切片提供了计算容量的方法 `cap()` 可以测量切片最长可以达到多少。

以下为具体实例：
```go
package main

import "fmt"

func main() {
   var numbers = make([]int,3,5)    //长度为3，容量为5

   printSlice(numbers)
}

func printSlice(x []int){
   fmt.Printf("len=%d cap=%d slice=%v\n",len(x),cap(x),x)
}
```
以上实例运行输出结果为:

```go
len=3 cap=5 slice=[0 0 0]
```

## 8.4. 空(nil)切片
一个切片在未初始化之前默认为 nil，长度为 0，实例如下：
```go
package main

import "fmt"

func main() {
   var numbers []int

   printSlice(numbers)

   if(numbers == nil){
      fmt.Printf("切片是空的")
   }
}

func printSlice(x []int){
   fmt.Printf("len=%d cap=%d slice=%v\n",len(x),cap(x),x)
}
```
以上实例运行输出结果为:
```go
len=0 cap=0 slice=[]    //切片是空的
```


## 8.5. 切片截取
可以通过设置下限及上限来设置截取切片 `[lower-bound:upper-bound]`，实例如下：
```go
package main

import "fmt"

func main() {
   /* 创建切片 */
   numbers := []int{0,1,2,3,4,5,6,7,8}   
   printSlice(numbers)

   /* 打印原始切片 */
   fmt.Println("numbers ==", numbers)

   /* 打印子切片从索引1(包含) 到索引4(不包含)*/
   fmt.Println("numbers[1:4] ==", numbers[1:4])

   /* 默认下限为 0*/
   fmt.Println("numbers[:3] ==", numbers[:3])

   /* 默认上限为 len(s)*/
   fmt.Println("numbers[4:] ==", numbers[4:])

   numbers1 := make([]int,0,5)
   printSlice(numbers1)

   /* 打印子切片从索引  0(包含) 到索引 2(不包含) */
   number2 := numbers[:2]
   printSlice(number2)

   /* 打印子切片从索引 2(包含) 到索引 5(不包含) */
   number3 := numbers[2:5]
   printSlice(number3)

}

func printSlice(x []int){
   fmt.Printf("len=%d cap=%d slice=%v\n",len(x),cap(x),x)
}
```
执行以上代码输出结果为：
```go
len=9 cap=9 slice=[0 1 2 3 4 5 6 7 8]
numbers == [0 1 2 3 4 5 6 7 8]
numbers[1:4] == [1 2 3]
numbers[:3] == [0 1 2]
numbers[4:] == [4 5 6 7 8]
len=0 cap=5 slice=[]
len=2 cap=9 slice=[0 1]
len=3 cap=7 slice=[2 3 4]
```

## 8.6. append() 和 copy() 函数
如果想增加切片的容量，我们**必须创建一个新的更大的切片并把原分片的内容都拷贝过来**。

下面的代码描述了从拷贝切片的 `copy()` 方法和向切片追加新元素的 `append()` 方法:
```go
package main

import "fmt"

func main() {
   var numbers []int
   printSlice(numbers)

   /* 允许追加空切片 */
   numbers = append(numbers, 0)
   printSlice(numbers)

   /* 向切片添加一个元素 */
   numbers = append(numbers, 1)
   printSlice(numbers)

   /* 同时添加多个元素 */
   numbers = append(numbers, 2,3,4)
   printSlice(numbers)

   /* 创建切片 numbers1 是之前切片的两倍容量*/
   numbers1 := make([]int, len(numbers), (cap(numbers))*2)

   /* 拷贝 numbers 的内容到 numbers1 */
   copy(numbers1,numbers)
   printSlice(numbers1)   
}

func printSlice(x []int){
   fmt.Printf("len=%d cap=%d slice=%v\n",len(x),cap(x),x)
}
```
以上代码执行输出结果为：
```go
len=0 cap=0 slice=[]
len=1 cap=1 slice=[0]
len=2 cap=2 slice=[0 1]
len=5 cap=6 slice=[0 1 2 3 4]
len=5 cap=12 slice=[0 1 2 3 4]
```

# 9. Go 语言-范围(Range)
Go 语言中 `range` 关键字用于 for 循环中迭代`数组(array)、切片(slice)、通道(channel)或集合(map)`的元素。在数组和切片中它**返回元素的索引和索引对应的值**，在集合中返回 `key-value 对`的 key 值。


这是我们使用range去求一个slice的和。使用数组跟这个很类似
```go
    nums := []int{2, 3, 4}
    sum := 0
    for _, num := range nums {
        sum += num
    }
    fmt.Println("sum:", sum)
    //sum: 9
```

在数组上使用`range`将传入`index`和`值`两个变量。上面那个例子我们不需要使用该元素的序号，所以我们使用空白符"`_`"省略了。有时侯我们确实需要知道它的索引。

```go
    for i, num := range nums {
        if num == 3 {
            fmt.Println("index:", i)
        }
    }
    //index: 1
```

`range`也可以用在`map`的`键值对`上。

```go
    kvs := map[string]string{"a": "apple", "b": "banana"}
    for k, v := range kvs {
        fmt.Printf("%s -> %s\n", k, v)
    }
    //a -> apple
    //b -> banana
```
`range`也可以用来枚举Unicode字符串。第一个参数是字符的索引，第二个是字符（Unicode的值）本身。
```go
    for i, c := range "go" {
        fmt.Println(i, c)
    }
    //0 103
    //1 111
```


# 10. Go 语言-Map(集合)
`Map` 是一种**无序**的**键值对的集合**。Map 最重要的一点是通过` key `来快速检索数据，`key` 类似于索引，指向数据的值。

`Map` 是一种集合，所以我们可以像迭代数组和切片那样迭代它。不过，Map 是无序的，我们无法决定它的返回顺序，这是因为 `Map` 是使用 hash 表来实现的。

## 10.1. 定义 Map
可以使用内建函数 `make` 也可以使用 `map` 关键字来定义 `Map`:

```go
/* 声明变量，默认 map 是 nil */
var map_variable map[key_data_type]value_data_type

/* 使用 make 函数 */
map_variable := make(map[key_data_type]value_data_type)
```
如果不初始化 `map`，那么就会创建一个 `nil map`。`nil map` **不能用来存放键值对**

**实例**
下面实例演示了创建和使用`map`:

```go
package main

import "fmt"

func main() {
    var countryCapitalMap map[string]string /*创建集合 */
    countryCapitalMap = make(map[string]string)

    /* map插入key - value对,各个国家对应的首都 */
    countryCapitalMap [ "France" ] = "Paris"
    countryCapitalMap [ "Italy" ] = "罗马"
    countryCapitalMap [ "Japan" ] = "东京"
    countryCapitalMap [ "India " ] = "新德里"

    /*使用键输出地图值 */ for country := range countryCapitalMap {
        fmt.Println(country, "首都是", countryCapitalMap [country])
    }

    /*查看元素在集合中是否存在 */
    captial, ok := countryCapitalMap [ "美国" ] 
    /*如果确定是真实的,则存在,否则不存在 */
    /*fmt.Println(captial) */
    /*fmt.Println(ok) */
    if (ok) {
        fmt.Println("美国的首都是", captial)
    } else {
        fmt.Println("美国的首都不存在")
    }
}
```
**注意这句： captial, ok := countryCapitalMap [ "美国" ] 
返回结果是两个，第二个是一个布尔值。**
以上实例运行结果为：

```go
France 首都是 Paris
Italy 首都是 罗马
Japan 首都是 东京
India  首都是 新德里
美国的首都不存在
```


## 10.2. delete() 函数
`delete()` 函数用于删除集合的元素, 参数为 `map` 和其对应的 `key`。
实例如下：

```go
package main

import "fmt"

func main() {
    /* 创建map */
    countryCapitalMap := map[string]string{"France": "Paris", "Italy": "Rome", "Japan": "Tokyo", "India": "New delhi"}

    fmt.Println("原始地图")

    /* 打印地图 */
    for country := range countryCapitalMap {
        fmt.Println(country, "首都是", countryCapitalMap [ country ])
    }

    /*删除元素*/ delete(countryCapitalMap, "France")
    fmt.Println("法国条目被删除")

    fmt.Println("删除元素后地图")

    /*打印地图*/
    for country := range countryCapitalMap {
        fmt.Println(country, "首都是", countryCapitalMap [ country ])
    }
}
```

以上实例运行结果为：
```go
原始地图
India 首都是 New delhi
France 首都是 Paris
Italy 首都是 Rome
Japan 首都是 Tokyo
法国条目被删除
删除元素后地图
Italy 首都是 Rome
Japan 首都是 Tokyo
India 首都是 New delhi
```

# 11. Go interface

接口是一种约定，它是一个抽象的类型，和我们见到的具体的类型如int、map、slice等不一样。具体的类型，我们可以知道它是什么，并且可以知道可以用它做什么；但是接口不一样，接口是抽象的，它**只有一组接口方法**，我们**并不知道它的内部实现**，所以我们不知道接口是什么，但是我们知道**可以利用它提供的方法做什么**。

抽象就是接口的优势，它不用和具体的实现细节绑定在一起，**我们只需定义接口，告诉编码人员它可以做什么**，这样我们可以**把具体实现分开**，这样编码就会更加灵活方面，适应能力也会非常强。

## 11.1. interface定义与实现的模板 实例1

```go
/* 首先，定义接口 */
type interface_name interface {
   method_name1 [return_type]
   method_name2 [return_type]
   method_name3 [return_type]
   ...
   method_namen [return_type]
}

/* 其次，定义结构体 */
type struct_name struct {
   /* variables */
}

/* 结构体类型struct_name，作为接收者实现接口方法 */
func (struct_name_variable struct_name) method_name1() [return_type] {
   /* 方法实现 */
}
...
func (struct_name_variable struct_name) method_namen() [return_type] {
   /* 方法实现*/
}
```

## 11.2. interface使用 实例2
```go
package main

import (
    "fmt"
)

//定义接口
type Phone interface {
    call()
}

//定义结构体类型NokiaPhone
type NokiaPhone struct {
}

//结构体类型NokiaPhone作为接收者，实现了接口中的方法call()，call()成为类型NokiaPhone的方法
func (nokiaPhone NokiaPhone) call() {
    fmt.Println("I am Nokia, I can call you!")
}

type IPhone struct {
}

func (iPhone IPhone) call() {
    fmt.Println("I am iPhone, I can call you!")
}

func main() {
    var phone Phone

    phone = new(NokiaPhone)
    phone.call()

    phone = new(IPhone)
    phone.call()

}
```

在上面的例子中，我们定义了一个接口`Phone`，接口里面有一个方法`call()`。然后我们在`main`函数里面定义了一个`Phone`类型变量，并分别为之赋值为`NokiaPhone`和`IPhone`。然后调用`call()`方法，输出结果如下：

```go
I am Nokia, I can call you!
I am iPhone, I can call you!
```

## 11.3. interface实例3


```go
#xiaorui.cc
package main

import "fmt"

type S struct {
    i int
}

func (p *S) Get() int {
    return p.i
}
func (p *S) Put(v int) {
    p.i = v
}

type I interface {
    Get() int
    Put(int)
}

func f1(p I) {
    fmt.Println(p.Get())
    p.Put(888)
}

func f2(p interface{}) {
    switch t := p.(type) {
    case int:
        fmt.Println("this is int number")
    case I:
        fmt.Println("I:", t.Get())
    default:
        fmt.Println("unknow type")
    }
}

//指针修改原数据，引用传递   *S
func dd(a *S) {
    a.Put(999)
    fmt.Println(a.Get(), "in dd func")
}

//临时数据，值传递   S
func aa(a S) {
    a.Put(2222)
    fmt.Println(a.Get(), "in aa func")
}

func main() {
    var s S
    s.Put(333)
    fmt.Println(s.Get())        //1
    f1(&s)                      //2
    fmt.Println(s.Get())        //3
    f2(&s)                      //4
    dd(&s)                      //5
    fmt.Println(s.Get())        //6
    aa(s)                       //7
    fmt.Println(s.Get())        //8

}

```
运行后结果：
```go
333        //1 

333         //2

888         //3

I: 888      //4

999 in dd func  //5

999             //6

2222 in aa func   //7

999             //8
```

## 11.4. Q1：为什么s可以直接调用Put函数

首先`s`是用`S`结构体创建的，`S`有`Get()、Put()`两个方法。所以`s`可以执行`Put()`

## 11.5. Q2：f函数为什么也能Get()

因为`S`实现了`I`类型的interface，换句话说，`S`实现了`I interface`类型定义好的方法，那么`I`定义也就有了`Get()`方法。



## 11.6. Q3：f2的interface{}空接口用途

`interface{}`空接口可以是任何类型，我们可以在逻辑用断言的方式区别他是什么类型，然后根据类型做相应的处理。对应到上面的代码， 我给你给他传任何值，f2因为是空接口都会接收进来。
后面的 `t := p.(type)`是断言，所谓的断言就是区分他的`type`类型。

- 空接口可代表任何类型，可做**形参**和**返回类型**

## 11.7. Q4：f1 f2 为什么要使用指针?

- 
- 存疑
- 

# 12. 理解Go interface的6个关键点

## 12.1. interface 是一种类型

```go  
type I interface {
    Get() int
}
```

首先 `interface` 是一种类型，从它的定义可以看出来用了 `type `关键字，更准确的说 `interface` 是一种具有一组方法的类型，这些方法定义了 `interface` 的行为。

go **允许不带任何方法**的 `interface` ，这种类型的 `interface` 叫 `empty interface`。

如果一个类型实现了一个 `interface` 中所有方法，我们说类型实现了该 `interface`，所以所有类型都实现了 `empty interface`，因为任何一种类型至少实现了` 0`个方法。go 没有显式的关键字用来实现 `interface`，只需要实现 `interface` 包含的方法即可。


## 12.2. interface 变量存储的是实现者的值

```go
//1
type I interface {    
    Get() int
    Set(int)
}
//2
type S struct {
    Age int
}
func(s S) Get()int {
    return s.Age
}
func(s *S) Set(age int) {
    s.Age = age
}
//3
func f(i I){
    i.Set(10)
    fmt.Println(i.Get())
}
func main() {
    s := S{} 
    f(&s)  //4
}
```
这段代码在 `#1` 定义了 `interface I`，在 `#2` 用 `struct S` 实现了 接口`I` 定义的两个方法，接着在 `#3` 定义了一个函数 `f` 参数类型是 `I`，`S` 实现了 `I` 的两个方法就说 `S` 是 `I` 的实现者，执行 `f(&s)` 就完了一次 `interface` 类型的使用。

`interface` 的重要用途就体现在`函数 f` 的参数中，如果有多种类型实现了某个 `interface`，这些类型的值都可以直接使用 `interface` 的变量存储。


把用户定义的类型称之为`实体类型`。

我们可以定义很多类型，让它们实现一个接口，那么这些类型都可以赋值给这个接口，这时候接口方法的调用，其实就是对应`实体类型`对应方法的调用，这就是`多态`。
```go
type animal interface {
	printInfo()
}
type cat int
type dog int
func (c cat) printInfo(){
	fmt.Println("a cat")
}
func (d dog) printInfo(){
	fmt.Println("a dog")
}

func main() {
	var a animal
	var c cat
	a=c
	a.printInfo()       //a cat
	//使用另外一个类型赋值
	var d dog
	a=d
	a.printInfo()       //a dog
}

```
以上例子演示了一个多态。我们定义了一个接口`animal`,然后定义了两种类型`cat`和`dog`实现了接口`animal`。在使用的时候，分别把类型`cat`的值`c`、类型`dog`的值`d`赋值给接口`animal`的值`a`,然后分别执行`a`的`printInfo`方法，可以看到不同的输出。

不难看出 `interface` 的变量中存储的是实现了 `interface` 的类型的对象值，这种能力是 `duck typing`。在使用 `interface` 时不需要显式在 `struct` 上声明要实现哪个 `interface` ，只需要实现对应 `interface` 中的方法即可，go 会自动进行 `interface` 的检查，并在运行时执行从其他类型到 `interface` 的自动转换，即使实现了多个 `interface`，go 也会在使用对应 `interface` 时实现自动转换，这就是 `interface` 的魔力所在。


## 12.3. 如何判断 interface 变量存储的是哪种类型

接着上一小节，我们看下`interface`的值被赋值后，`interface`值内部的布局。`interface`的值是一个**两个字长度**的数据结构：

- 第一个字包含一个指向内部表结构的指针，这个内部表里存储的有`实体类型`的信息以及相关联的`方法集`；
- 第二个字包含的是一个指向`存储的实体类型值`的指针。

所以`interface`的值结构其实是两个指针，这也可以说明`interface`其实一个引用类型。

一个` interface` 被多种类型实现时，有时候我们需要区分` interface` 的变量究竟存储哪种类型的值，go 可以使用 `comma, ok `的形式做区分。

`value, ok := em.(T)`：`em` 是 `interface` 类型的变量，`T`代表要断言的类型，`value `是 `interface` 变量存储的值，`ok` 是 `bool` 类型表示是否为该断言的类型` T`。

```go
if t, ok := i.(*S); ok {
    fmt.Println("s implements I", t)
}
```

`ok` 是` true` 表明 `i` 存储的是 `*S `类型的值，`false` 则不是，这种区分能力叫` Type assertions (类型断言)`。

如果需要区分多种类型，可以使用` switch `断言，更简单直接，这种断言方式只能在 `switch` 语句中使用。

```go
switch t := i.(type) {
case *S:
    fmt.Println("i store *S", t)
case *R:
    fmt.Println("i store *R", t)
}
```

## 12.4. 实现接口的方法集：指针接收者、值接收者

我们都知道，如果要实现一个接口，必须实现这个接口提供的所有方法，但是实现方法的时候，我们可以使用**指针接收者**实现，也可以使用**值接收者**实现，这两者是有区别的，下面我们就好好分析下这两者的区别。

### 12.4.1. 值接收者

```go
type animal interface {
	printInfo()
}

//需要一个animal接口作为参数
func invoke(a animal){
	a.printInfo()
}

type cat int
//值接收者实现animal接口
func (c cat) printInfo(){
	fmt.Println("a cat")
}

func main() {
	var c cat
	//值作为参数传递
	invoke(c)
}
```

还是原来的例子改改，增加一个`invoke`函数，该函数接收一个`animal`接口类型的参数，例子中传递参数的时候，也是以类型`cat`的值`c`传递的，运行程序可以正常执行。

**接下来，我们稍微改造一下，使用类型`cat`的指针`&c`作为参数传递。**

```go
func main() {
	var c cat
	//指针作为参数传递
	invoke(&c)
}
```
只修改这一处，其他保持不变，我们运行程序，发现也可以正常执行。

通过这个例子我们可以得出结论：实体类型以**值接收者**实现接口的时候，不管是**实体类型的值**，还是**实体类型值的指针**，**都实现了该接口**。

### 12.4.2. 指针接收者

```go
func main() {
	var c cat
	//值作为参数传递
	invoke(c)
}
//需要一个animal接口作为参数
func invoke(a animal){
	a.printInfo()
}
type animal interface {
	printInfo()
}
type cat int
//指针接收者实现animal接口
func (c *cat) printInfo(){
	fmt.Println("a cat")
}
```
这个例子中把实现接口的接收者改为指针，但是传递参数的时候，我们还是按值进行传递，点击运行程序，会出现以下异常提示：

```go
./main.go:10: cannot use c (type cat) as type animal in argument to invoke:
    cat does not implement animal (printInfo method has pointer receiver)
```
提示中已经很明显的告诉我们，说`cat`没有实现`animal`接口，因为`printInfo`方法有一个指针接收者，所以`cat`类型的值`c`不能作为接口类型`animal`传参使用。

**关键点**是 `cat` 中 `printInfo` 方法的 `receiver` 是个 `pointer *cat` 

`interface` 定义时并没有严格规定实现者的方法 `receiver` 是个 `value receiver` 还是 `pointer receiver`，上面代码中的 `cat` 的 `cat receiver` 是 `pointer`，也就是实现 `interface animal` 的方法的 `receiver` 是 `pointer`，使用 `invoke(c)`的形势调用，传递给 `invoke` 的是个 `c` 的一份拷贝，在进行 `c` 的拷贝到 `animal` 的转换时，`s` 的拷贝不满足 `Set` 方法的 `receiver` 是个 `pointer`，也就没有实现 `I`。、

**go 中函数都是按值传递即 `passed by value(值传递)`。**

**下面我们再稍微修改下，改为以指针作为参数传递。**

```go
func main() {
	var c cat
	//指针作为参数传递
	invoke(&c)
}
```
其他都不变，只是把以前使用值的参数，改为使用指针作为参数，我们再运行程序，就可以正常运行了。

由此可见实体类型以 **指针接收者** 实现接口的时候，**只有指向这个类型的指针**才被认为实现了该接口

### 12.4.3. 小结：

首先，以 **方法接收者** 是值还是指针的角度看。

|Methods| Receivers Values|
|:-----|:----- |
|(t T)	|T and *T|
|(t *T)	 | *T  |

上面的表格可以解读为：
- **如果是值接收者，实体类型的值和指针都可以实现对应的接口；**
- **如果是指针接收者，那么只有类型的指针能够实现对应的接口。**

其次，我们我们以 **实体类型** 是值还是指针的角度看。

|Values|	Methods Receivers|
|:-----|:----- |
|T	|(t T)|
|*T	|(t T) and (t *T)|

上面的表格可以解读为：
- **类型的值只能实现值接收者的接口；**
- **指向类型的指针，既可以实现值接收者的接口，也可以实现指针接收者的接口。**

### 12.4.4. 再举个例子

```go
//1
type I interface {    
    Get() int
    Set(int)
}
//2
type S struct {
    Age int
}
func(s S) Get()int {
    return s.Age
}
func(s *S) Set(age int) {
    s.Age = age
}
//3
func f(i I){
    i.Set(10)
    fmt.Println(i.Get())
}
func main() {
    s := S{} 
    f(&s)  //4
}
```

这段代码在 #1 定义了 `interface I`，在 #2 用 `struct S` 实现了 `I` 定义的两个方法，接着在 #3 定义了一个函数 `f` 参数类型是 `I`，`S` 实现了 `I` 的两个方法就说 `S` 是 `I` 的实现者，执行 `f(&s)` 就完了一次 `interface` 类型的使用。

在这个例子中调用 `f` 是 `f(&s)` 也就是` S `的指针类型，为什么不能是 `f(s) `呢，如果是 `s` 会有什么问题？改成 `f(s)` 然后执行代码。

```go
cannot use s (type S) as type I in argument to f:
    S does not implement I (Set method has pointer receiver)
```
这个错误的意思是 `S` 没有实现 `I`，哪里出了问题？关键点是 `S` 中 `set` 方法的 `receiver` 是个 `pointer *S` 。

`interface` 定义时并没有严格规定实现者的方法 `receiver` 是个 `value receiver` 还是 `pointer receiver`，上面代码中的 `S` 的 `Set receiver` 是 `pointer`，也就是实现 `I` 的两个方法的 `receiver` 一个是 `value` 一个是 `pointer`，使用 `f(s)`的形势调用，传递给 `f` 的是个 `s` 的一份拷贝，在进行 `s` 的拷贝到 `I` 的转换时，`s` 的拷贝不满足 `Set` 方法的 `receiver` 是个 `pointer`，也就没有实现 `I`。
**go 中函数都是按值传递即 passed by vappoooppplue。**

**那反过来会怎样，如果 receiver 是 value，函数用 pointer 的形式调用？**

```go
type I interface {
	Get() int
	Set(int)
}
type SS struct {
	Age int
}
func (s SS) Get() int {
	return s.Age
}
func (s SS) Set(age int) {
	s.Age = age
}
func f(i I) {
	i.Set(10)
	fmt.Println(i.Get())
}
func main(){
  	ss := SS{}
	f(&ss) //ponter
	f(ss)  //value
}
```
`I` 的实现者 `SS` 的方法 `receiver` 都是 `value receiver`，执行代码可以看到无论是 `pointer` 还是 `value` 都可以正确执行。

**导致这一现象的原因**是什么？

如果是按 `pointer` 调用，**go 会自动进行转换，因为有了指针总是能得到指针指向的值是什么**，如果是 `value` 调用，go 将无从得知 `value` 的原始值是什么，因为 `value` 是份拷贝。go 会把指针进行隐式转换得到 `value`，但反过来则不行。

对于 `receiver` 是 `value` 的 `method`，任何在 `method` 内部对 `value` 做出的改变都不影响调用者看到的 `value`，这就是按值传递。

## 12.5. 空的 interface
`interface{}` 是一个空的 `interface` 类型，根据前文的定义：一个类型如果实现了一个 `interface` 的所有方法就说该类型实现了这个 `interface`，空的 `interface` 没有方法，所以可以认为所有的类型都实现了 `interface{}`。如果定义一个函数参数是 `interface{}` 类型，这个函数应该可以接受任何类型作为它的参数。

```go
func doSomething(v interface{}){    
}
```

如果函数的参数 `v `可以接受任何类型，那么函数被调用时在函数内部 `v` 是不是表示的是任何类型？并不是，虽然函数的参数可以接受任何类型，并不表示 `v` 就是任何类型，在函数 `doSomething` 内部` v` 仅仅是一个 `interface` 类型，之所以函数可以接受任何类型是在 go 执行时传递到函数的任何类型都被`自动转换成 interface{}`。 **go 是如何进行转换的，以及 v 存储的值究竟是怎么做到可以接受任何类型的，感兴趣的可以看看 [Russ Cox 关于 interface 的实现](https://research.swtch.com/interfaces)**。

既然空的 `interface` 可以接受任何类型的参数，那么一个 `interface{}`类型的 `slice` 是不是就可以接受任何类型的 `slice` ?

```go
func printAll(vals []interface{}) { //1
	for _, val := range vals {
		fmt.Println(val)
	}
}
func main(){
	names := []string{"stanley", "david", "oscar"}
	printAll(names)
}
```


```go
报错：cannot use names (type []string) as type []interface {} in argument to printAll
```

这个错误说明 go 没有帮助我们自动把 `slice` 转换成 `interface{}` 类型的 `slice`，所以出错了。go 不会对 类型是`interface{}` 的 slice 进行转换 。为什么 go 不帮我们自动转换，在 go 的 wiki 中找到了答案 https://github.com/golang/go/wiki/InterfaceSlice 
大意是 `interface{}` 会占用两个字长的存储空间，`一个是自身的 methods 数据`，`一个是指向其存储值的指针`，也就是 interface 变量存储的值，因而 `slice []interface{}` 其长度是固定的N*2，但是 `[]T` 的长度是`N*sizeof(T)`，两种 slice 实际存储值的大小是有区别的(文中只介绍两种 slice 的不同，至于为什么不能转换猜测可能是 runtime 转换代价比较大)。

但是我们可以手动进行转换来达到我们的目的。

```go
var dataSlice []int = foo()
var interfaceSlice []interface{} = make([]interface{}, len(dataSlice))
for i, d := range dataSlice {
	interfaceSlice[i] = d
}
```


# 13. Go 嵌入类型

嵌入类型，或者嵌套类型，这是一种可以把已有的类型声明在新的类型里的一种方式，这种功能对代码复用非常重要。

在其他语言中，有继承可以做同样的事情，但是在Go语言中，没有继承的概念，**Go提倡的代码复用的方式是组合**，所以这也是嵌入类型的意义所在，组合而不是继承，所以Go才会更灵活。

## 13.1. 用于接口类型
```go
type Reader interface {
	Read(p []byte) (n int, err error)
}

type Writer interface {
	Write(p []byte) (n int, err error)
}

type Closer interface {
	Close() error
}

type ReadWriter interface {
	Reader
	Writer
}

type ReadCloser interface {
	Reader
	Closer
}

type WriteCloser interface {
	Writer
	Closer
}
```

以上是标准库io包里，我们常用的接口，可以看到`ReadWriter`接口是嵌入`Reader`和`Reader`接口而组合成的新接口，这样我们就不用重复的定义被嵌入接口里的方法，直接通过嵌入就可以了。

## 13.2. 用于结构体类型
嵌入类型同样适用于结构体类型，我们再来看个例子

```go
type user struct {
	name string
	email string
}

type admin struct {
	user
	level string
}
```

嵌入后，被嵌入的类型称之为内部类型、新定义的类型称之为外部类型，这里
- `user`是**内部类型**；
- `admin`是**外部类型**。

通过嵌入类型，**与内部类型相关联的所有字段、方法、标志符等等所有，都会被外包类型所拥有**，就像外部类型自己的一样，这就达到了代码快捷复用组合的目的，而且定义非常简单，只需声明这个类型的名字就可以了。

**同时，外部类型还可以添加自己的方法、字段属性**等，可以很方便的扩展外部类型的功能。

## 13.3. 嵌入类型的使用

```go
func main() {
	ad:=admin{user{"张三","zhangsan@flysnow.org"},"管理员"}
	fmt.Println("可以直接调用,名字为：",ad.name)
	fmt.Println("也可以通过内部类型调用,名字为：",ad.user.name)
	fmt.Println("但是新增加的属性只能直接调用，级别为：",ad.level)
}
```


以上是嵌入类型的使用，可以看到，我们在**初始化的时候，采用的是字面值的方式，所以要按其定义的结构进行初始化**，**先初始化user这个内部类型的，再初始化新增的level 属性**。

对于内部类型的属性和方法访问上，我们可以用外部类型直接访问，也可以通过内部类型进行访问；但是我们为外部类型新增的方法属性字段，只能使用外部类型访问，因为内部类型没有这些。

## 13.4. 外部类型覆盖内部类型

当然，外部类型也可以声明同名的字段或者方法，来覆盖内部类型的东西，这种情况方法比较多，我们**以方法为例：**

```go
func main() {
	ad:=admin{user{"张三","zhangsan@flysnow.org"},"管理员"}
	ad.user.sayHello()
	ad.sayHello()
}

type user struct {
	name string
	email string
}

type admin struct {
	user
	level string
}

func (u user) sayHello(){
	fmt.Println("Hello，i am a user")
}

func (a admin) sayHello(){
	fmt.Println("Hello，i am a admin")
}
```

内部类型`user`有一个`sayHello`方法，外部类型对其进行了覆盖，同名重写`sayHello`，然后我们在main方法里分别访问这两个类型的方法，打印输出:

```go
Hello，i am a user
Hello，i am a admin
```

从输出中看，方法`sayHello`被成功覆盖了。

嵌入类型的强大，还体现在：**如果内部类型实现了某个接口，那么外部类型也被认为实现了这个接口**。我们稍微改造下例子看下。


```go
func main() {
	ad:=admin{user{"张三","zhangsan@flysnow.org"},"管理员"}
	sayHello(ad.user)//使用user作为参数
	sayHello(ad)//使用admin作为参数
}

type user struct {
	name string
	email string
}

type admin struct {
	user
	level string
}

type Hello interface {
	hello()
}

func (u user) hello(){
	fmt.Println("Hello，i am a user")
}

func sayHello(h Hello){
	h.hello()
}
```

这个例子原来的结构体类型`user`和`admin`的定义不变，**新增了一个接口`Hello`**,然后让`user`类型实现这个接口，最后我们定义了一个`sayHello方法`，它接受一个`Hello`接口类型的参数，最终我们在main函数演示的时候，发现不管是`user`类型，还是`admin`类型作为参数传递给`sayHello方法`的时候，都可以正常调用。

这里就可以说明`admin`实现了接口`Hello`,但是我们又没有显式的声明类型`admin`实现，所以这个实现是通过内部类型`user`实现的，因为`admin`包含了`user`所有的方法函数，所以也就实现了`接口Hello`。

当然外部类型也可以重新实现，只需要像上面例子一样覆盖同名的方法即可。这里要说明的是，**不管我们如何同名覆盖，都不会影响内部类型**，我们还可以通过访问内部类型来访问它的方法、属性字段等。

嵌入类型的定义，是Go为了方便我们扩展或者修改已有类型的行为，是为了宣传组合这个概念而设计的，所以我们经常使用组合，灵活运用组合，扩展出更多的我们需要的类型结构。


# 14. Go 反射


## 14.1. TypeOf和ValueOf
在Go的反射定义中，任何接口都会由两部分组成的:
- 一个是接口的具体类型;
- 一个是具体类型对应的值。

在Go反射中，标准库为我们提供两种类型来分别表示他们:
- reflect.Value
- reflect.Type

并且提供了两个函数来获取任意对象的Value和Type:
- reflect.TypeOf()
- reflect.ValueOf()


```go
func main() {
	u:= User{"张三",20}
	t:=reflect.TypeOf(u)
    fmt.Println(t)          //out: main.User
    
    v:=reflect.ValueOf(u)
    fmt.Println(v)          //out: {张三 20}

    //简便的方法
    fmt.Printf("%T\n",u)    //out: main.User
    fmt.Printf("%v\n",u)    //out: {张三 20}
}
type User struct{
	Name string
	Age int
}
```



## 14.2. 获取类型底层类型
底层的类型：其实对应的主要是**基础类型，接口、结构体、指针**这些，因为我们可以通过type关键字声明很多新的类型，比如上面的例子，对象u的实际类型是User，但是对应的底层类型是struct这个结构体类型。


## 14.3. 遍历字段和方法

通过反射，我们可以**获取一个结构体类型的字段**,也可以**获取一个类型的导出方法**，这样我们就可以在**运行时**了解一个类型的结构，这是一个非常强大的功能。
主要使用的方法：
- `NumField()`：获取结构体有多少个字段
- `NumMethod()`：获取结构体有多少个方法
- `Field(i)`：传递上面两个方法得到的索引

```go
//打印出结构体的所有字段名以及该结构体的方法
for i:=0;i<t.NumField();i++ {
	fmt.Println(t.Field(i).Name)
}
for i:=0;i<t.NumMethod() ;i++  {
	fmt.Println(t.Method(i).Name)
}
```

## 14.4. 修改字段的值
在运行中动态的修改某个字段的值,有两种方法：
- 一种就是常规的有提供的方法或者导出的字段可以供我们修改，
- 一种是使用反射。

举个栗子：
```go
func main() {
	x:=2
	v:=reflect.ValueOf(&x)
	v.Elem().SetInt(100)
	fmt.Println(x)
}
```

以上就是通过反射修改一个变量的例子。

必须确保以下几个重点，才可以保证值可以被修改：

- 1. 因为reflect.ValueOf函数返回的是一份值的拷贝，所以前提是我们是传入要修改变量的地址。
- 2. 其次需要我们调用Elem方法找到这个指针指向的值。
- 3. 最后我们就可以使用SetInt方法修改值了。

## 14.5. 动态调用方法

结构体的方法我们不光可以正常的调用，还可以使用反射进行调用。要想反射调用，我们`先要获取到需要调用的方法`，`然后进行传参调用`，如下示例：

```go
func main() {
	u:=User{"张三",20}
	v:=reflect.ValueOf(u)
	mPrint:=v.MethodByName("Print")
	args:=[]reflect.Value{reflect.ValueOf("前缀")}
	fmt.Println(mPrint.Call(args))
}
type User struct{
	Name string
	Age int
}
func (u User) Print(prfix string){
	fmt.Printf("%s:Name is %s,Age is %d",prfix,u.Name,u.Age)
}
```

`MethodByName`方法可以让我们**根据一个方法名获取一个方法对象**，**然后**我们构建好该方法需要的参数，**最后**调用Call就达到了动态调用方法的目的。

获取到的方法我们可以使用`IsValid` 来判断是否可用（存在）。

这里的参数是一个`Value`类型的数组，所以需要的参数，我们必须要`通过ValueOf函数进行转换`。

# 15. Go Struct Tag

## 15.1. JSON字符串对象转换

在上一节介绍Go反射的时候，提到了如何通过反射获取Struct的Tag，这一篇文章主要就是介绍这个的使用和原理，在介绍之前我们先看一下JSON字符串和Struct类型相互转换的例子。

```go
func main() {
	var u User
	h:=`{"name":"张三","age":15}`
	err:=json.Unmarshal([]byte(h),&u)
	if err!=nil{
		fmt.Println(err)
	}else {
		fmt.Println(u)
	}
}
type User struct{
	Name string `name`
	Age int `age`
}
```

上面这个例子就是`Json字符串转User对象`的例子，这里主要利用的就是`User`这个结构体对应的字段`Tag`，json解析的原理就是通过`反射`获得`每个字段的tag`，然后把解析的json`对应的值`赋给他们。

利用字段`Tag`不光可以把`Json字符串转为结构体对象`，还可以把`结构体对象转为Json字符串`。请看下面的例子：

```go
newJson,err:=json.Marshal(&u)
fmt.Println((string(newJson)))
```

## 反射获取字段Tag

字段的`Tag`是标记到字段上的，所以我们可以通过先获取字段，然后再获取字段上的`Tag`。

```go
func main() {
	var u User
	t:=reflect.TypeOf(u)
	for i:=0;i<t.NumField();i++{
		sf:=t.Field(i)
		fmt.Println(sf.Tag)
	}
}
```

获取字段上一节我们提到过，获取字段后，调用`.Tag`就获取到对应的`Tag`字段了。


## 字段Tag的键值对

很多时候我们的一个`Struct`不止具有一个功能，比如我们需要`JSON`的互转、还需要`BSON`以及`ORM`解析的互转，所以一个字段可能对应多个不同的`Tag`，以便满足不同的功能场景。

Go Struct 为我们提供了`键值对的Tag`，来满足我们以上的需求。

```go
func main() {
	var u User
	t:=reflect.TypeOf(u)
	for i:=0;i<t.NumField();i++{
		sf:=t.Field(i)
		fmt.Println(sf.Tag.Get("json"))
	}
}

type User struct{
	Name string `json:"name"`
	Age int `json:"age"`
}
```
以上的例子，使用了`键值对`的方式配置`Struct Tag`，`Key-Value`以冒号分开，这里的`Key`为`json`，所以我们可以通过这个`Key`获取对应的值，也就是通过`.Tag.Get("json"))`方法。`Get`方法就是通过一个`Key`获取对应的`tag`设置。

除此之外，我们还可以设置多个Key，来满足我们上面说的场景。

```go
func main() {
	var u User
	t:=reflect.TypeOf(u)
	for i:=0;i<t.NumField();i++{
		sf:=t.Field(i)
		fmt.Println(sf.Tag.Get("json"),",",sf.Tag.Get("bson"))
	}
}

type User struct{
	Name string `json:"name" bson:"b_name"`
	Age int `json:"age" bson:"b_age"`
}
```

`多个Key`使用空格进行分开，然后使用`Get()`方法获取不同`Key`的值。

`Struct Tag`可以提供字符串到`Struct`的映射能力，以便我们作转换，除此之外，还可以作为字段的元数据的配置，提供我们需要的配置，比如生成`Swagger`文档等。


