# Golang 学习笔记

## 0 好书

**系统书籍：**

- [x] [Go语言圣经](https://github.com/golang-china/gopl-zh)
- [x] Go语言实战
- [ ] Tony 白 go语言第一课
- [x] 郝林Go语言核心36讲
- [ ] 孔令飞 Go语言项目开发实战
- [ ] Go语言学习笔记-雨痕
- [ ] [Go入门指南](https://github.com/unknwon/the-way-to-go_ZH_CN/blob/master/eBook/directory.md)
- [ ] [Go Web 编程](https://github.com/astaxie/build-web-application-with-golang)
- [ ] 数据密集型应用系统设计
- [ ] python
- [ ] 数据库：mysql,redis,mongodb
- [ ] https://juejin.cn/post/7050081105063444494

**go规范：**

- [x] [Effective Go](https://go.dev/doc/effective_go#names)：高效 Go 编程，由 Golang 官方编写，里面包含了编写 Go 代码的一些建议，也可以理解为最佳实践。
- [x] [Go Code Review Comments](https://github.com/golang/go/wiki/CodeReviewComments)：Golang 官方编写的 Go 最佳实践，作为 Effective Go 的补充。
- [x] [Uber Go 语言编码规范](https://github.com/xxjwxc/uber_go_guide_cn)
- [x] [Style guideline for Go packages](https://rakyll.org/style-packages/)：包含了如何组织 Go 包、如何命名 Go 包、如何写 Go 包文档的一些建议。
- [ ] [Go语言规范文档](https://golang.google.cn/ref/spec)

好书：

- [ ] [微服务设计](https://weread.qq.com/web/reader/b6e325705dd9ecb6e246423)
- [ ] [Docker技术入门与实战](https://weread.qq.com/web/reader/57f327107162732157facd6)
- [ ] **[What every computer science major should know](http://matt.might.net/articles/what-cs-majors-should-know/)**

## RoadMap

![www.golangroadmap.com_.png](https://gitee.com/mouri11/image/raw/master/0c464997790bb62c6063700b407bcf5f.png)

![img](https://gitee.com/mouri11/image/raw/master/c702df29da67be3c4083ecce1d0eadb7.png)

![img](https://gitee.com/mouri11/image/raw/master/add8566dc5431378bda313a32a6ebb85.jpg)

![img](https://gitee.com/mouri11/image/raw/master/29c794731d9fb43f9fd37cc4d91994f8.png)

## 1 基础篇

### 小知识

#### 基础

- 命名：包本身的名字一般总是用小写字母。Go语言程序员推荐使用 **驼峰式** 命名。

- 声明：Go语言主要有四种类型的**声明语句**：var、const、type和func。包一级的各种类型的声明语句的顺序无关紧要（译注：函数内部的名字则必须先声明之后才能使用）。

- 变量

  - 接口或引用类型（包括slice、指针、map、chan和函数）变量对应的零值是nil。数组或结构体等聚合类型对应的零值是每个元素或字段都是对应该类型的零值。

  - Go 语言里的引用类型有如下几个:切片、映射、通道、接口和函数类型。当声明上述类型的变量时，创建的变量被称作标头(header)值。从技术细节上说，字符串也是一种引用类型。

  - 在包级别声明的变量会在main入口函数执行前完成初始化（§2.6.2），局部变量将在声明语句被执行到的时候完成初始化。

  - := 简短变量声明语句对这些已经声明过的变量就只有赋值行为了。

  - 每次我们对一个变量取地址，或者复制指针，我们都是为原变量创建了新的别名。指针特别有价值的地方在于我们可以不用名字而访问一个变量，但是这是一把双刃剑：要找到一个变量的所有访问者并不容易，我们必须知道变量全部的别名（译注：这是Go语言的垃圾回收器所做的工作）。不仅仅是指针会创建别名，很多其他引用类型也会创建别名，例如slice、map和chan，甚至结构体、数组和接口都会创建所引用变量的别名。

  - ```go
    img.SetColorIndex(
            size+int(x*size+0.5), size+int(y*size+0.5),
            blackIndex, // 最后插入的逗号不会导致编译错误，这是Go编译器的一个特性
        )               // 小括弧另起一行缩进，和大括弧的风格保存一致
    ```

  - 同时为了防止编译器在行尾自动插入分号而导致的编译错误，可以在末尾的参数变量后面显式插入逗号。最后插入的逗号不会导致编译错误，这是Go编译器的一个特性

  - 对于在包一级声明的变量来说，它们的生命周期和整个程序的运行周期是一致的。而相比之下，局部变量的生命周期则是动态的：每次从创建一个新变量的声明语句开始，直到该变量不再被引用为止，然后变量的存储空间可能被回收。

  - 那么Go语言的自动垃圾收集器是如何知道一个变量是何时可以被回收的呢？这里我们可以避开完整的技术细节，基本的实现思路是，从每个包级的变量和每个当前运行函数的每一个局部变量开始，通过指针或引用的访问路径遍历，是否可以找到该变量。如果不存在这样的访问路径，那么说明该变量是不可达的，也就是说它是否存在并不会影响程序后续的计算结果。

  - 因为一个变量的有效周期只取决于是否可达，因此一个循环迭代内部的局部变量的生命周期可能超出其局部作用域。同时，局部变量可能在函数返回之后依然存在。编译器会自动选择在栈上还是在堆上分配局部变量的存储空间，但可能令人惊讶的是，这个选择并不是由用var还是new声明变量的方式决定的。

  - 任何时候，创建一个变量并初始化为其零值，习惯是使用关键字 var。这种用法是为了更明 确地表示一个变量被设置为零值。如果变量被初始化为某个非零值，就配合结构字面量和短变量 声明操作符来创建变量。

- 包package

  - 在每个源文件的包声明前紧跟着的注释是包注释（§10.7.4）。通常，包注释的第一句应该先是包的功能概要说明。一个包通常只有一个源文件有包注释（译注：如果有多个包注释，目前的文档工具会根据源文件名的先后顺序将它们链接为一个包注释）。如果包注释很大，通常会放到一个独立的doc.go文件中。

  - 包的初始化：包的初始化首先是解决包级变量的依赖顺序，然后按照包级变量声明出现的顺序依次初始化。init初始化函数除了不能被调用或引用外，其他行为和普通函数类似。在每个文件中的init初始化函数，在程序开始执行时按照它们声明的顺序被自动调用。每个包在解决依赖的前提下，以导入声明的顺序初始化，每个包只会被初始化一次。因此，如果一个p包导入了q包，那么在p包初始化的时候可以认为q包必然已经初始化过了。初始化工作是自下而上进行的，main包最后被初始化。以这种方式，可以确保在main函数执行之前，所有依赖的包都已经完成初始化工作了。

  - ```go
    var a = b + c // a 第三个初始化, 为 3
    var b = f()   // b 第二个初始化, 为 2, 通过调用 f (依赖c)
    var c = 1     // c 第一个初始化, 为 1
    
    func f() int { return c + 1 }
    ```

  - 当前包的其它源文件无法访问在当前源文件导入的包。

- Go基础数据类型-数字、字符串、布尔型、常量

  - Go语言将数据类型分为四类：基础类型、复合类型、引用类型和接口类型。本章介绍基础类型，包括：数字、字符串和布尔型。复合数据类型——数组（§4.1）和结构体（§4.2）——是通过组合简单类型，来表达更加复杂的数据结构。引用类型包括指针（§2.3.2）、切片（§4.2)）、字典（§4.3）、函数（§5）、通道（§8），虽然数据种类很多，但它们都是对程序中一个变量或状态的间接引用。这意味着对任一引用类型数据的修改都会影响所有该引用的拷贝。

  - ```go
    var z float64
    fmt.Println(z, -z, 1/z, -1/z, z/z) // "0 -0 +Inf -Inf NaN"
    ```

  - `&&`的优先级比`||`高（助记：`&&`对应逻辑乘法，`||`对应逻辑加法，乘法比加法优先级要高）

  - 字符串的值是不可变的：一个字符串包含的字节序列永远不会被改变，因此尝试修改字符串内部数据的操作也是被禁止的

  - ASCII码->unicode码->utf-8(变长的unicode码)

  - 常量表达式的值在编译期计算，而不是在运行期。每种常量的潜在类型都是基础类型：boolean、string或数字。

- 复合数据类型-数组、slice、map和结构体

  - slice的底层确实引用一个数组对象。一个slice由三个部分构成：指针、长度和容量。

  - 所有的Go语言函数应该以相同的方式对待nil值的slice和0长度的slice。

  - 切片扩容：如果切片的容量小于1024个元素,那么扩容的时候slice的cap就翻番,乘以2;一旦元素个数超过1024个元素，增长因子就变成1.25，即每次增加原来容量的四分之一。

  - 禁止对map元素取址的原因是map可能随着元素数量的增长而重新分配更大的内存空间，从而可能导致之前的地址无效。

  - Map的迭代顺序是不确定的，并且不同的哈希函数实现可能导致不同的遍历顺序

  - ```go
    import "sort"
    // 顺序遍历key/value
    var names []string
    for name := range ages {
        names = append(names, name)
    }
    sort.Strings(names)
    for _, name := range names {
        fmt.Printf("%s\t%d\n", name, ages[name])
    }
    ```

  - 结构体匿名成员：外层的结构体不仅仅是获得了匿名成员类型的所有成员，而且也获得了该类型导出的全部的方法。这个机制可以用于将一些有简单行为的对象组合成有复杂行为的对象。组合是Go语言中面向对象编程的核心。
  
  - 通过嵌入类型，与内部类型相关的标识符会提升到外部类型上。这些被提升的标识符就像直 接声明在外部类型里的标识符一样，也是外部类型的一部分。这样外部类型就组合了内部类型包 含的所有属性，并且可以添加新的字段和方法。外部类型也可以通过声明与内部类型标识符同名 的标识符来覆盖内部标识符的字段或者方法。这就是扩展或者修改已有类型的方法。

#### 函数

  - 你可能会偶尔遇到没有函数体的函数声明，这表示该函数不是以Go实现的。这样的声明定义了函数签名。

    ```Go
    package math
    
    func Sin(x float64) float //implemented in assembly language
    ```

  - Go程序员也把函数值叫做闭包。**「函数」和「函数内部能访问到的变量」的总和，就是一个闭包。**闭包常常用来「间接访问一个变量」。换句话说，「隐藏一个变量」。

  - defer关键字，多个defer语句时，先声明的最后执行。**被推迟函数的实参（如果该函数为方法则还包括接收者）在推迟执行时就会被求值， 而不是在调用执行时才求值。**这样不仅无需担心变量值在函数执行时被改变， 同时还意味着单个被推迟的调用可推迟多个函数的执行。

    ```go
    // bigSlowOperation被调时，trace会返回一个函数值，该函数值会在bigSlowOperation退出时被调用。通过这种方式， 我们可以只通过一条语句控制函数的入口和所有的出口，甚至可以记录函数的运行时间，如例子中的start。
    func bigSlowOperation() {
        defer trace("bigSlowOperation")() // don't forget the extra parentheses
        // ...lots of work…
        time.Sleep(10 * time.Second) // simulate slow operation by sleeping
    }
    func trace(msg string) func() {
        start := time.Now()
        log.Printf("enter %s", msg)
        return func() { 
            log.Printf("exit %s (%s)", msg,time.Since(start)) 
        }
    }
    ```

  - 为了方便诊断问题，runtime包允许程序员输出堆栈信息。将panic机制类比其他语言异常机制的读者可能会惊讶，runtime.Stack为何能输出已经被释放函数的信息？在Go的panic机制中，延迟函数的调用在释放堆栈信息之前。

    ```go
    func main() {
        defer printStack()
        f(3)
    }
    func printStack() {
        var buf [4096]byte
        n := runtime.Stack(buf[:], false)
        os.Stdout.Write(buf[:n])
    }
    ```

  - defer、return、返回值三者的执行逻辑应该是：return最先执行，return负责将结果写入返回值中；接着defer开始执行一些收尾工作；最后函数携带当前返回值退出。为了弄清上述两种情况的区别，我们首先要理解return 返回值的运行机制:return 并非原子操作，分为赋值，和返回值两步操作，无名返回值 : 实际上return 执行了两步操作，因为返回值没有命名，所以
    return 默认指定了一个返回值（假设为s），首先将i赋值给s,后续的操作因为是针对i,进行的，所以不会影响s, 此后因为s不会更新，所以return s 不会改变；有名返回值 : 因为返回值已经提前定义了,不会产生临时零值变量, 返回值就是提前定义的变量,后续所有的操作也都是基于已经定义的变量, 任何对于返回值变量的修改都会影响到返回值本身。

  - recover:recover会使程序从panic中恢复，并返回panic value。在未发生panic时调用recover，recover会返回nil。考虑到语言解析器的复杂性，即使某个语言解析器目前工作正常，也无法肯定它没有漏洞。因此，当某个异常出现时，我们不会选择让解析器崩溃，而是会将panic异常当作普通的解析错误，并附加额外信息提醒用户报告此错误。

  - recover:通过recover处理panic时，必须在引发panic的当前协程就处理掉，否则待其传递到父协程直至main方法都不能通过recover处理。如果被恢复的panic是通过调用panic函数引发的，那么它返回的结果值就是我们传给panic函数参数值的副本。

  - panic只能触发当前goroutine 的 defer 调用。在defer调用中只要存在recover ，就能处理其抛出的“恐慌”事件。需要注意的是，其他goroutine中的defer对其不起作用，即不支持跨协程调用。 

  - 想要捕获或处理panic造成的“恐慌”事件，recover必须与defer配套使用，否则无效。在Go语言中，是存在一些无法恢复的致命错误方法的，如fatalthrow方法和fatalpanic方法等，它们一般在并发写入map等处理时抛出，需要谨慎。 

  - net/http包中提供了一个web服务器，将收到的请求分发给用户提供的处理函数。很显然，我们不能因为某个处理函数引发的panic异常，杀掉整个进程；web服务器遇到处理函数导致的panic时会调用recover，输出堆栈信息，继续运行。这样的做法在实践中很便捷，但也会引起资源泄漏，或是因为recover操作，导致其他问题。

  - 每个包可以包含**任意多个 init 函数**，这些函数都会在程序执行开始的时候被调用。所有被 编译器发现的 init 函数都会安排在 main 函数之前执行。init 函数用在设置包、初始化变量 或者其他要在程序运行前优先完成的引导工作。

#### 方法

- 我们可以给同一个包内的任意命名类型定义方法，只要这个命名类型的底层类型不是指针或者interface。只有类型（Point）和指向他们的指针`(*Point)`，才可能是出现在接收器声明里的两种接收器。此外，为了避免歧义，在声明方法时，如果一个类型名本身是一个指针的话，是不允许其出现在接收器中的。
  
- 当你定义一个允许nil作为接收器值的方法的类型时，注意判nil；
  
      ```go
      func (list *IntList) Sum() int {
          if list == nil {
              return 0
          }
          return list.Value + list.Tail.Sum()
      }
      ```
  
- 我们经常选择一个方法，并且在同一个表达式里执行，比如常见的p.Distance()形式，实际上将其分成两步来执行也是可能的。p.Distance叫作“选择器”，选择器会返回一个方法“值”->一个将方法（Point.Distance）绑定到特定接收器变量的函数。这个函数可以不通过指定其接收器即可被调用；即调用时不需要指定接收器（译注：因为已经在前文中指定过了），只要传入函数的参数即可：
  
      ```go
      p := Point{1, 2}
      distanceFromP := p.Distance        // method value 方法值
      fmt.Println(distanceFromP(q)) 
      ```
  
    - 函数和方法的区别是指有没有接收器，而不像其他语言那样是指有没有返回值。
    
    - 和方法“值”相关的还有方法表达式。当调用一个方法时，与调用一个普通的函数相比，我们必须要用选择器（p.Distance）语法来指定方法的接收器。当T是一个类型时，方法表达式可能会写作`T.f`或者`(*T).f`，会返回一个函数“值”，这种函数会将其第一个参数用作接收器，所以可以用通常（译注：不写选择器）的方式来对其进行调用：
    
      ```go
      p := Point{1, 2}
      q := Point{4, 6}
      
      distance := Point.Distance   // method expression 方法表达式
      fmt.Println(distance(p, q))  // "5"
      ```
  
- 一个对象的变量或者方法如果对调用方是不可见的话，一般就被定义为“封装”。封装有时候也被叫做信息隐藏，同时也是面向对象编程最关键的一个方面。
  
- 这种基于名字（首字母大写导出，小写隐藏）的手段使得在语言中最小的封装单元是package，而不是像其它语言一样的类型。一个struct类型的字段对同一个包的所有代码都有可见性，无论你的代码是写在一个函数还是一个方法里。
  
- 因为大部分方法在被调用后都需要维护接收者的值的状态，所以，一个最佳实践是，将方法 的接收者声明为指针。
  
 - 与直接通过值或者指针调用方法不同，如果通过接口类型的值调用方法，规则有很大不同， 如代码清单 2-38 所示。使用指针作为接收者声明的方法，只能在接口类型的值是一个指针的时 候被调用。使用值作为接收者声明的方法，在接口类型的值为值或者指针时，都可以被调用。

#### 接口

- 很多面向对象的语言都有相似的接口概念，但Go语言中接口类型的独特之处在于它是满足隐式实现的。也就是说，我们没有必要对于给定的具体类型定义所有满足的接口类型；简单地拥有一些必需的方法就足够了。这种设计可以让你创建一个新的接口类型满足已经存在的具体类型却不会去改变这些类型的定义；当我们使用的类型来自于不受我们控制的包时这种设计尤其有用。

- 实际上interface{}被称为空接口类型是不可或缺的。因为空接口类型对实现它的类型没有要求，所以我们可以将任意一个值赋给空接口类型。我们当然不能直接对它持有的值做操作，因为interface{}没有任何方法。我们会在7.10章中学到一种用类型断言来获取interface{}中值的方法。

- 不像基于类的语言，他们一个类实现的接口集合需要进行显式的定义(implements)，在Go语言中我们可以在需要的时候定义一个新的抽象或者特定特点的组，而不需要修改具体类型的定义。当具体的类型来自不同的作者时这种方式会特别有用。当然也确实没有必要在具体的类型中指出这些共性。

- 接口值：概念上讲一个接口的值，接口值，由两个部分组成，一个具体的类型和那个类型的值。它们被称为接口的动态类型和动态值。在Go语言中，变量总是被一个定义明确的值初始化，即使接口类型也不例外。对于一个接口的零值就是它的类型和值的部分都是nil（图7.1）。

- 接口值可以使用==和!＝来进行比较。两个接口值相等仅当它们都是nil值，或者它们的动态类型相同并且动态值也根据这个动态类型的==操作相等。因为接口值是可比较的，所以它们可以用在map的键或者作为switch语句的操作数。然而，如果两个接口值的动态类型相同，但是这个动态类型是不可比较的（比如切片），将它们进行比较就会失败并且panic。考虑到这点，接口类型是非常与众不同的。其它类型要么是安全的可比较类型（如基本类型和指针）要么是完全不可比较的类型（如切片，映射类型，和函数），但是在比较接口值或者包含了接口值的聚合类型时，我们必须要意识到潜在的panic。同样的风险也存在于使用接口作为map的键或者switch的操作数。只能比较你非常确定它们的动态值是可比较类型的接口值。

- 类型断言是一个使用在接口值上的操作。语法上它看起来像x.(T)被称为断言类型，这里x表示一个接口的类型和T表示一个类型。一个类型断言检查它操作对象的动态类型是否和断言的类型匹配。

- 在最简单的形式中，一个类型分支像普通的switch语句一样，它的运算对象是x.(type)——它使用了关键词字面量type——并且每个case有一到多个类型。一个类型分支基于这个接口值的动态类型使一个多路分支有效。这个nil的case和if x == nil匹配，并且这个default的case和如果其它case都不匹配的情况匹配。一个对sqlQuote的类型分支可能会有这些case：

  ```go
  switch x.(type) {
  case nil:       // ...
  case int, uint: // ...
  case bool:      // ...
  case string:    // ...
  default:        // ...
  }
  ```

- 一些建议：当设计一个新的包时，新手Go程序员总是先创建一套接口，然后再定义一些满足它们的具体类型。这种方式的结果就是有很多的接口，它们中的每一个仅只有一个实现。不要再这么做了。这种接口是不必要的抽象；它们也有一个运行时损耗。你可以使用导出机制（§6.6）来限制一个类型的方法或一个结构体的字段是否在包外可见。接口只有当有两个或两个以上的具体类型必须以相同的方式进行处理时才需要。当一个接口只被一个单一的具体类型实现时有一个例外，就是由于它的依赖，这个具体类型不能和这个接口存在在一个相同的包中。这种情况下，一个接口是解耦这两个包的一个好方式。因为在Go语言中只有当两个或更多的类型实现一个接口时才使用接口，它们必定会从任意特定的实现细节中抽象出来。结果就是有更少和更简单方法的更小的接口（经常和io.Writer或 fmt.Stringer一样只有一个）。当新的类型出现时，小的接口更容易满足。对于接口设计的一个好的标准就是 ask only for what you need（只考虑你需要的东西）

- 命名接口的时候，也需要遵守 Go 语言的命名惯例。如果接口类型只包含一个方法，那么这 个类型的名字以 er 结尾。我们的例子里就是这么做的，所以这个接口的名字叫作 Matcher。如 果接口类型内部声明了多个方法，其名字需要与其行为关联。

- 接口值是一个两个字长度 的数据结构，第一个字包含一个指向内部表的指针。这个内部表叫作 iTable，包含了所存储的 值的类型信息。iTable 包含了已存储的值的类型信息以及与这个值相关联的一组方法。第二个 字是一个指向所存储值的指针。将类型信息和指针组合在一起，就将这两个值组成了一种特殊 的关系。

- 接口完整性检查 ：go编译器并没有严格检查一个对象是否实现了某接口所有方法，常见的做法是声明一个_变量`var _ Repository = (*Dao)(nil)`，这样如果没有实现所有相关的接口，编译器就会报错。

- [类型选择](https://go-zh.org/doc/effective_go.html#type_switch) 是类型转换的一种形式：它接受一个接口，在选择 （switch）中根据其判断选择对应的情况（case）， 并在某种意义上将其转换为该种类型。以下代码为 fmt.Printf 通过类型选择将值转换为字符串的简化版。若它已经为字符串，我们需要该接口中实际的字符串值； 若它有 String 方法，我们则需要调用该方法所得的结果。

  ```go
  type Stringer interface {
      String() string
  }
  
  var value interface{} // Value provided by caller.
  switch str := value.(type) {
  case string:
      return str
  case Stringer:
      return str.String()
  }
  ```

  若我们只关心一种类型呢？若我们知道该值拥有一个 string 而想要提取它呢？ 只需一种情况的类型选择就行，但它需要类型断言。类型断言接受一个接口值， 并从中提取指定的明确类型的值。其语法借鉴自类型选择开头的子句，但它需要一个明确的类型， 而非 type 关键字：`value.(typeName)`。但若它所转换的值中不包含字符串，该程序就会以运行时错误崩溃。为避免这种情况， 需使用 “逗号, ok” 惯用法来测试它能安全地判断该值是否为字符串：若类型断言失败，str 将继续存在且为字符串类型，但它将拥有零值，即空字符串。

  ```go
  str, ok := value.(string)
  if ok {
      fmt.Printf("string value is: %q\n", str)
  } else {
      fmt.Printf("value is not a string\n")
  }
  ```

- 

#### 包和工具

- 在 Go 语言里，包是个非常重要的概念。其设计理念是使用包来封装不同 语义单元的功能。这样做，能够更好地复用代码，并对每个包内的数据的使用有更好的控制。
- 所有的.go 文件，除了空行和注释，都应该在第一行声明自己所属的包。每个包都在一个单 独的目录里。不能把多个包放到同一个目录中，也不能把同一个包的文件分拆到多个不同目录中。 这意味着，同一个目录下的所有.go 文件必须声明同一个包名。
- 给包及其目录命名 时，应该使用简洁、清晰且全小写的名字，这有利于开发时频繁输入包名。例如，net/http 包 下面的包，如 cgi、httputil 和 pprof，名字都很简洁。

- 导入的包之间可以通过添加空行来分组；通常将来自不同组织的包独自分组。包的导入顺序无关紧要，但是在每个分组中一般会根据字符串顺序排列。（gofmt和goimports工具都可以将不同分组导入的包独立排序。）
- 选择另一个包名称还可以帮助避免和本地普通变量名产生冲突。例如，如果文件中已经有了一个名为path的变量，那么我们可以将“path”标准包重命名为pathpkg。
- 要尽量避免包名使用可能被经常用于局部变量的名字，这样可能导致用户重命名导入包，例如前面看到的path包。
- 一个internal包只能被和internal目录有同一个父目录的包所导入。
- 编译器就会按照下面的顺序查找包，一旦编译器找到一个满足 import 语句的包，就停止进一步查找。有一件重要的事需要记 住，编译器会首先查找 Go 的安装目录，然后才会按顺序查找 GOPATH 变量里列出的目录。如果编译器查遍 GOPATH 也没有找到要导入的包，那么在试图对程序执行 run 或者 build 的时候就会出错。本章后面会介绍如何通过go get命令来修正这种错误。
- go get 将获取任意指定的 URL 的包，或者一个已经导入的包所依赖的其 他包。由于go get的这种递归特性，这个命令会扫描某个包的源码树，获取能找到的所有依赖包。
- go vet 命令会帮开发人 员检测代码的常见错误。go vet 工具不能让开发者避免严重的逻辑错误，或者避免编写充满小错的代码。不过，正 像刚才的实例中展示的那样，这个工具可以很好地捕获一部分常见错误。每次对代码先执行 go vet 再将其签入源代码库是一个很好的习惯。
- go fmt 命令会 自动格式化开发人员指定的源代码文件并保存。
- go doc 可以在终端上直接使用 go doc 命令来打印文档。godoc -http=:6060这个命令通知 godoc 在端口 6060 启动 Web 服务器。如果浏览器已经打开，导航到 http://localhost:6060 可以看到一个页面，包含所有 Go 标准库和你的 GOPATH 下的 Go 源代码的文档。
- go build -race // 用竞争检测器标志来编译程序

#### 测试

- 在包目录内，所有以`_test.go`为后缀名的源文件在执行go build时不会被构建成包的一部分，它们是go test测试的一部分。
- 在`*_test.go`文件中，有三种类型的函数：测试函数、基准测试（benchmark）函数、示例函数。一个测试函数是以Test为函数名前缀的函数，用于测试程序的一些逻辑行为是否正确；go test命令会调用这些测试函数并报告测试结果是PASS或FAIL。基准测试函数是以Benchmark为函数名前缀的函数，它们用于衡量一些函数的性能；go test命令会多次运行基准测试函数以计算一个平均的执行时间。示例函数是以Example为函数名前缀的函数，提供一个由编译器保证正确性的示例文档。
- go test命令会遍历所有的`*_test.go`文件中符合上述命名规则的函数，生成一个临时的main包用于调用相应的测试函数，接着构建并运行、报告测试结果，最后清理测试中生成的临时文件。
- 每个测试函数必须导入testing包。测试函数的名字必须以Test开头，可选的后缀名必须以大写字母开头。
- `go test`命令如果没有参数指定包那么将默认采用当前目录对应的包（和`go build`命令一样）。
- 一种测试分类的方法是基于测试者是否需要了解被测试对象的内部工作原理。黑盒测试只需要测试包公开的文档和API行为，内部实现对测试代码是透明的。相反，白盒测试有访问包内部函数和数据结构的权限，因此可以做到一些普通客户端无法实现的测试。例如，一个白盒测试可以在每个操作之后检测不变量的数据类型。（白盒测试只是一个传统的名称，其实称为clear box测试会更准确。）
- 示例代码的函数名字必须基于已经存在的公开的函数或 者方法。写示例代码的目的是展示某个函数或者方法的特定使用方法。
- 基准测试是一种测试代码性能的方法。想要测试解决同一问题的不同方案的性能，以及查看 哪种解决方案的性能更好时，基准测试就会很有用。可以看到第一个基准测试函数，名为 BenchmarkSprintf。 基准测试函数必须以 Benchmark 开头

#### error

建议使用"github.com/pkg/errors"工具包中的方法

对于区分error类型的常规做法：**利用多态**

对于github常规的error，打印日志级别为error。

对于ecode里面的error，打印warn级别。

### slice 和 map

**slice和map使用前必须make，养成习惯**

slice

- 无论是nil slice还是empty slice，都可以对它们进行操作，如append()函数、len()函数和cap()函数。

- ```go
  // nil slice 
  var slice []int
  slice[1] = 0 //会报数组越界的错误
  ```

map

- 使用前必须make

#### 切片

- 切片是一个很小的对象，这 3 个字段分别是指向底层数组的指针、切片访问的元素的个数(即长度)和切片允许增长 到的元素个数(即容量)。
- 不管是使用 nil 切片还是空切片，对其调用内置函数 append、len 和 cap 的效果都是一样的。
- 函数 append 会智能地处理底层数组的容量增长。在切片的容量小于 1000 个元素时，总是 会成倍地增加容量。一旦元素个数超过 1000，容量的增长因子会设为 1.25，也就是会每次增加 25% 的容量。随着语言的演化，这种增长算法可能会有所改变。

#### map

- 在 Go 语言里，通过键来索引映射时，即便这个键不存在也总会返回一个值。在这种情况下， 返回的是该值对应的类型的零值。
- 对映射来说，range 返回的不是索引和值，而是键值对。
- 数组是构造切片和映射的基石。
- 通过组合，可以创建多维数组和多维切片。也可以使用切片或者其他映射作为映射的值。但是切片不能用作映射的键。

### make 和 new

golang **分配内存**主要有内置函数new和make.

既然 new 返回的内存已置零，那么当你设计数据结构时， 每种类型的零值就不必进一步初始化了，这意味着该数据结构的使用者只需用 new 创建一个新的对象就能正常工作。表达式 new(File) 和 &File{} 是等价的。

make，只能为slice, map, channel分配内存。

再回到内存分配上来。内建函数 make(T, args) 的目的不同于 new(T)。它只用于创建切片、映射和信道，并返回类型为 T（而非 `*T`）的一个已初始化 （而非置零）的值。 出现这种差异的原因在于，这三种类型本质上为引用数据类型，它们在使用前必须初始化。

### gorutine

- Go 标准库的 runtime 包里有一个名为 GOMAXPROCS 的函数， 通过它可以指定调度器可用的逻辑处理器的数量。用这个函数，可以给每个可用的物理处理器在 运行的时候分配一个逻辑处理器。
- 并发(concurrency)不是并行(parallelism)。并行是让不同的代码片段同时在不同的物理处 理器上执行。并行的关键是同时做很多事情，而并发是指同时管理很多事情，这些事情可能只做 了一半就被暂停去做别的事情了。在很多情况下，并发的效果比并行好，因为操作系统和硬件的 总资源一般很少，但能支持系统同时做很多事情。这种“使用较少的资源做更多的事情”的哲学， 也是指导 Go 语言设计的哲学。如果希望让 goroutine 并行，必须使用多于一个逻辑处理器。当有多个逻辑处理器时，调度器会将 goroutine 平等分配到每个逻辑处理器上。这会让 goroutine 在不同的线程上运行。不过要想真 的实现并行的效果，用户需要让自己的程序运行在有多个物理处理器的机器上。否则，哪怕 Go 语 言运行时使用多个线程，goroutine 依然会在同一个物理处理器上并发运行，达不到并行的效果。
- 只有在有多个逻辑处理器且可以同时让每个 goroutine 运行在一个可用的物理处理器上的时候，goroutine 才会并行运行。
- runtime 包的 Gosched 函数，用于将 goroutine 从当前线程退出， 给其他 goroutine 运行的机会。
- Go 语言提供了传统的同步 goroutine 的机制，就是对共享资源加锁。如果需要顺序访问一个 整型变量或者一段代码，atomic 和 sync 包里的函数提供了很好的解决方案。
- 如果 哪个 doWork goroutine 试图在 main 函数调用 StoreInt64 的同时调用 LoadInt64 函数，那 么原子函数会将这些调用互相同步，保证这些操作都是安全的，不会进入竞争状态。
- 在 Go 语言里，你不仅可以使用原子函数和互斥锁来保证对共享资源的安全访 问以及消除竞争状态，还可以使用通道，通过发送和接收需要共享的资源，在 goroutine 之间做 同步。
- goroutine 在逻辑处理器上执行，而逻辑处理器具有独立的系统线程和运行队列。
- 原子函数和互斥锁提供了一种防止出现竞争状态的办法。通道提供了一种在两个 goroutine 之间共享数据的简单方法。
- 每个 goroutine 的初始栈大小仅为 2k；由 Go 运行时而不是操作系统调度，goroutine 上下文切换在用户层完成，开销更小；在语言层面而不是通过标准库提供。goroutine 由go关键字创建，一退出就会被回收或销毁，开发体验更佳；
- 和线程一样，一个应用内部启动的所有 goroutine 共享进程空间的资源，如果多个 goroutine 访问同一块内存数据，将会存在竞争，我们需要进行 goroutine 间的同步。
- goroutine 的使用代价很低，Go 官方也推荐你多多使用 goroutine。而且，多数情况下，我们不需要考虑对 goroutine 的退出进行控制：goroutine 的执行函数的返回，就意味着 goroutine 退出。
- goroutine 执行的函数或方法即便有返回值，Go 也会忽略这些返回值。所以，如果你要获取 goroutine 执行后的返回值，你需要另行考虑其他方法，比如通过 goroutine 间的通信来实现。
- 传统语言的并发模型是基于对内存的共享的。CSP（Communicating Sequential Processes，通信顺序进程）并发模型。
- 从程序的整体结构来看，Go 始终推荐以 CSP 并发模型风格构建并发程序，尤其是在复杂的业务层面，这能提升程序的逻辑清晰度，大大降低并发设计的复杂性，并让程序更具可读性和可维护性。不过，对于局部情况，比如涉及性能敏感的区域或需要保护的结构体数据时，我们可以使用更为高效的低级同步原语（如 mutex），保证 goroutine 对数据的同步访问。

### channel

**本质是一个阻塞队列。**

`ch := make(chan int, 5)`   make`调用了`runtime.makechan.   查看汇编：go tool compile -N -l -S main.go

**从接受者队列获取一个接受者, 若存在, 数据直接发送, 不走缓存, 提高效率**

```go
// 其内部维护了一个循环队列(数组), 用于管理发送与接收的缓存数据. 
type hchan struct {
  // 队列中元素个数
	qcount   uint
  // 队列的大小(数组长度)
	dataqsiz uint
  // 指向底层的缓存队列, 是一个可以指向任意类型的指针. 
	buf      unsafe.Pointer
  // 管道每个元素的大小
	elemsize uint16
  // 是否被关闭了
	closed   uint32
  // 管道的元素类型
	elemtype *_type
  // 当前可以发送的元素索引(队尾)
	sendx    uint  
  // 当前可以接收的元素索引(队首)
	recvx    uint  
  // 当前等待接收数据的 goroutine 队列
	recvq    waitq
  // 当前等待发送数据的 goroutine 队列
	sendq    waitq 
	// 锁, 用来保证管道的每个操作都是原子性的. 
	lock mutex
}

// 发送
// 若无需阻塞且不能发送数据, 返回失败
// 若存在接收者, 直接发送数据
// 若存在缓存, 将数据放到缓存中
// 若无需阻塞, 返回失败
// 阻塞等待发送数据
func chansend(c *hchan, ep unsafe.Pointer, block bool, callerpc uintptr) bool {
  // 异常处理, 若管道指针为空
	if c == nil {
		if !block {
			return false
		}
		gopark(nil, nil, waitReasonChanSendNilChan, traceEvGoStop, 2)
		throw("unreachable")
	}
	// 常量判断, 恒为 false, 应该是开发时调试用的. 
	if debugChan {
		print("chansend: chan=", c, "\n")
	}
	// 常量, 恒为 false, 没看懂这个判断
	if raceenabled {
		racereadpc(c.raceaddr(), callerpc, funcPC(chansend))
	}
  // 若当前操作不阻塞, 且管道还没有关闭时判断
  // 当前队列容量为0且没有等待接收数据的 或 当前队列容量不为0且队列已满
  // 那么问题来了, 什么时候不加锁呢? select 的时候. 可以在不阻塞的时候快速返回
	if !block && c.closed == 0 && ((c.dataqsiz == 0 && c.recvq.first == nil) ||
		(c.dataqsiz > 0 && c.qcount == c.dataqsiz)) {
		return false
	}
	// 上锁, 保证操作的原子性
	lock(&c.lock)
	// 若管道已经关闭, 报错
	if c.closed != 0 {
		unlock(&c.lock)
		panic(plainError("send on closed channel"))
	}
	// 从接受者队列获取一个接受者, 若存在, 数据直接发送, 不走缓存, 提高效率
	if sg := c.recvq.dequeue(); sg != nil {
		send(c, sg, ep, func() { unlock(&c.lock) }, 3)
		return true
	}
	// 若缓存为满, 则将数据放到缓存中排队
	if c.qcount < c.dataqsiz {
    // 取出对尾的地址
		qp := chanbuf(c, c.sendx)
    // 将ep 的内容拷贝到 ap 地址
		typedmemmove(c.elemtype, qp, ep)
    // 更新队尾索引
		c.sendx++
		if c.sendx == c.dataqsiz {
			c.sendx = 0
		}
		c.qcount++
		unlock(&c.lock)
		return true
	}
	// 若当前不阻塞, 直接返回
	if !block {
		unlock(&c.lock)
		return false
	}
	// 当走到这里, 说明数据没有成功发送, 且需要阻塞等待. 
  // 以下代码没看懂, 不过可以肯定的是, 其操作为阻塞当前协程, 等待发送数据
	gp := getg()
	mysg := acquireSudog()
	mysg.releasetime = 0
	if t0 != 0 {
		mysg.releasetime = -1
	}
	mysg.elem = ep
	mysg.waitlink = nil
	mysg.g = gp
	mysg.isSelect = false
	mysg.c = c
	gp.waiting = mysg
	gp.param = nil
	c.sendq.enqueue(mysg)
	gopark(chanparkcommit, unsafe.Pointer(&c.lock), waitReasonChanSend, traceEvGoBlockSend, 2)
	KeepAlive(ep)
	if mysg != gp.waiting {
		throw("G waiting list is corrupted")
	}
	gp.waiting = nil
	gp.activeStackChans = false
	if gp.param == nil {
		if c.closed == 0 {
			throw("chansend: spurious wakeup")
		}
		panic(plainError("send on closed channel"))
	}
	gp.param = nil
	if mysg.releasetime > 0 {
		blockevent(mysg.releasetime-t0, 2)
	}
	mysg.c = nil
	releaseSudog(mysg)
	return true
}
```

**Tips:**

- **nil channel与closed channel:**
  nil channel：未初始化的channel，即没有执行make操作
  closed channel：执行了closed操作的channel
  - 对nil channel的读写会永久block
  - 向closed channel写入会发生panic
  - 从closed channel读取会立即读出零值
  - 试图重复关闭一个channel将导致panic异常，试图关闭一个nil值的channel也将导致panic异常。关闭一个channels还会触发一个广播机制。
- **发送器一旦停止发送数据后应该立即关闭channel**。
- `make(chan int)`默认buffer是0，是一个**同步**管道。 
- 不管一个channel是否被关闭，当它没有被引用时将会被Go语言的垃圾自动回收器回收。
- 无缓存channel更强地保证了每个发送操作与相应的同步接收操作；但是对于带缓存channel，这些操作是解耦的。
- 一个没有任何case的select语句写作select{}，会永远地等待下去。如果没有用标签就break的话只会退出内层的select循环，而外层的for循环会使之进入下一轮select循环。
- 无缓冲的通道保证进行发送和接收的 goroutine 会在同一时间进行数据交换;有缓冲的 通道没有这种保证。
- 当通道关闭后，goroutine 依旧可以从通道接收数据， 但是不能再向通道里发送数据。能够从已经关闭的通道接收数据这一点非常重要，因为这允许通 道关闭后依旧能取出其中缓冲的全部值，而不会有数据丢失。从一个已经关闭且没有数据的通道 里获取数据，总会立刻返回，并返回一个通道类型的零值。如果在获取通道时还加入了可选的标 志，就能得到通道的状态信息。
- 声明一个只有一端（发送端或者接收端）能用的通道没有任何意义。单向通道最主要的用途就是约束其他代码的行为。
- select语句只能与通道联用，它一般由若干个分支组成。每次执行这种语句的时候，一般只有一个分支中的代码会被运行。由于select语句是专为通道而设计的，所以每个case表达式中都只能包含操作通道的表达式。我们可能会因为通道关闭了，而直接从通道接收到一个其元素类型的零值。所以，在很多时候，我们需要通过接收表达式的第二个结果值来判断通道是否已经关闭。一旦发现某个通道关闭了，我们就应该及时地屏蔽掉对应的分支或者采取其他措施。这对于程序逻辑和程序性能都是有好处的。

### context

Go1.7之前，Go标准库还没有内置Context。

1. [Go语言实战笔记（二十）| Go Context](https://www.flysnow.org/2017/05/12/go-in-action-go-context.html)
2. go 1.7之前context的替代产品-https://github.com/gorilla/context。参考文章：http://jiagoushi.coder55.com/article/116640

Context类型之所以受到了标准库中众多代码包的积极支持，主要是因为它是一种非常通用的同步工具。它的值不但可以被任意地扩散，而且还可以被用来传递额外的信息和信号。更具体地说，Context类型可以提供一类代表上下文的值。此类值是并发安全的，也就是说它可以被传播给多个 goroutine。

Context 使用原则

- 不要把Context放在结构体中，要以参数的方式传递
- 以Context作为参数的函数方法，应该把Context作为第一个参数，放在第一位。
- 给一个函数方法传递Context的时候，不要传递nil，如果不知道传递什么，就使用context.TODO
- Context的Value相关方法应该传递必须的数据，不要什么数据都使用这个传递
- Context是线程安全的，可以放心的在多个goroutine中传递

### 基于共享变量的并发

**不要使用共享数据来通信；使用通信来共享数据。**

**并发和并行这两个词的含义是不同的，需要我们分清楚。简单来说，并发是指在同一个时间段内提交多个任务给系统，并行是指在同一时刻系统能执行多个任务。**

有三种方式可以避免数据竞争：

- 第一种方法是不要去写变量。
- 第二种避免数据竞争的方法是，避免从多个goroutine访问变量。**使用channel**
- 第三种避免数据竞争的方法是允许很多goroutine去访问变量，但是在同一个时刻最多只有一个goroutine在访问。这种方式被称为“互斥”。**使用sync包**

#### **sync.Mutex互斥锁**

- 我们使用了一个buffered channel作为一个计数信号量，来保证最多只有20个goroutine会同时执行HTTP请求。同理，我们可以用一个容量只有1的channel来保证最多只有一个goroutine在同一时刻访问一个共享变量。一个只能为1和0的信号量叫做二元信号量（binary semaphore）。

- defer调用只会比显式地调用Unlock成本高那么一点点，不过却在很大程度上保证了代码的整洁性。大多数情况下对于并发程序来说，代码的整洁性比过度的优化更重要。如果可能的话尽量使用defer来将临界区扩展到函数的结束。

- 解锁未锁定的互斥锁会立即引发 panic。并且，与死锁导致的 panic 一样，它们是无法被恢复的。因此，我们总是应该保证，对于每一个锁定操作，都要有且只有一个对应的解锁操作。换句话说，我们应该让它们成对出现。在很多时候，利用defer语句进行解锁可以更容易做到这一点。
- 不过要注意，Mutex类型是一个结构体类型，属于值类型中的一种。把它传给一个函数、将它从函数中返回、把它赋给其他变量、让它进入某个通道都会导致它的副本的产生。并且，原值和它的副本，以及多个副本之间都是完全独立的，它们都是不同的互斥锁。

使用互斥锁的注意事项如下：

- 不要重复锁定互斥锁；
- 不要忘记解锁互斥锁，必要时使用defer语句；
- 不要对尚未锁定或者已解锁的互斥锁解锁；
- 不要在多个函数之间直接传递互斥锁。
- 再次强调，我们总是应该让每一个互斥锁都只保护一个临界区，或一组相关临界区。

**sync.RWMutex**类型中的Lock方法和Unlock方法分别用于对写锁进行锁定和解锁，而它的RLock方法和RUnlock方法则分别用于对读锁进行锁定和解锁。

- 在写锁已被锁定的情况下再试图锁定写锁，会阻塞当前的 goroutine。
- 在写锁已被锁定的情况下试图锁定读锁，也会阻塞当前的 goroutine。
- 在读锁已被锁定的情况下试图锁定写锁，同样会阻塞当前的 goroutine。
- 在读锁已被锁定的情况下再试图锁定读锁，并不会阻塞当前的 goroutine。

换一个角度来说，对于某个受到读写锁保护的共享资源，多个写操作不能同时进行，写操作和读操作也不能同时进行，但多个读操作却可以同时进行。

go里没有重入锁，关于重入锁的概念，请参考java。

**go不支持重入锁的原因：**使用可重入锁是一种彻彻底底的错误，它是bug的温床。

其实 Russ Cox 于 2010 年在《Experimenting with GO[1]》就给出了答复，认为递归（又称：重入）互斥是个坏主意，这个设计并不好。

```go
func F() {
        mu.Lock()
        ... do some stuff ...
        G()
        ... do some more stuff ...
        mu.Unlock()
}
 
func G() {
        mu.Lock()
        ... do some stuff ...
        mu.Unlock()
}
```

在上述代码中，我们在 F 方法中调用 mu.Lock 方法加上了锁。如果支持可重入锁，接着就会进入到 G 方法中。

此时就会有一个致命的问题，你不知道 F 和 G 方法加锁后是不是做了什么事情，从而导致破坏了不变量，毕竟随手起几个协程做点坏事，也是完全可能的。

这对于 Go 是无法接受的，可重入的设计违反了前面所提到的设计理念，也就是：“要保证这些变量的不变性保持，不会在后续的过程中被破坏”。

基于上述原因，Go 官方团队选择了没有支持该项特性。

“同步”不仅仅是一堆goroutine执行顺序的问题，同样也会涉及到内存的问题。

#### **sync.WaitGroup**

- 之所以说WaitGroup值中计数器的值不能小于0，是因为这样会引发一个 panic。 不适当地调用这类值的Done方法和Add方法都会如此。

- 我们可以总结出这样一条关于WaitGroup值的使用禁忌，即：不要把增加其计数器值的操作和调用其Wait方法的代码，放在不同的 goroutine 中执行。换句话说，要杜绝对同一个WaitGroup值的两种操作的并发执行。

利用WaitGroup值，我们可以很方便地实现一对多的 goroutine 协作流程，即：一个分发子任务的 goroutine，和多个执行子任务的 goroutine，共同来完成一个较大的任务。在使用WaitGroup值的时候，我们一定要注意，千万不要让其中的计数器的值小于0，否则就会引发 panic。

我们最好用“先统一Add，再并发Done，最后Wait”这种标准方式，来使用WaitGroup值。 尤其不要在调用Wait方法的同时，并发地通过调用Add方法去增加其计数器的值，因为这也有可能引发 panic。

#### **sync.Once**

```go
func (o *Once) Do(f func()) {
	if atomic.LoadUint32(&o.done) == 0 {
		// Outlined slow-path to allow inlining of the fast-path.
		o.doSlow(f)
	}
}
func (o *Once) doSlow(f func()) {
	o.m.Lock()
	defer o.m.Unlock()
	if o.done == 0 {
		defer atomic.StoreUint32(&o.done, 1)
		f()
	}
}
```

1. sync.Once 会保证又一次调用成功之后再返回，也就是说如果多人同时调用`Do`函数，则只有一个人调用成功之后，其他人才会返回。
2. 当调用函数Panic时，也会认为其已经调用完成，其它调用方也会返回。

**Goroutine和线程**

每一个OS线程都有一个固定大小的内存块（一般会是2MB）来做栈，这个栈会用来存储当前正在被调用或挂起（指在调用其它函数时）的函数的内部变量。相反，一个goroutine会以一个很小的栈开始其生命周期，一般只需要2KB。一个goroutine的栈，和操作系统线程一样，会保存其活跃或挂起的函数调用的本地变量，但是和OS线程不太一样的是，一个goroutine的栈大小并不是固定的；栈的大小会根据需要动态地伸缩。而goroutine的栈的最大值有1GB，比传统的固定大小的线程栈要大得多，尽管一般情况下，大多goroutine都不需要这么大的栈。

#### sync.Pool

sync.Pool 用于缓存“可交换”、“可遗失”的对象。可交换的意思是就是，我用对象 A 也可以，用对象 B 也可以，无所谓。可遗失的意思是，存在里边的对象没了就没了，无所谓，我再创建一个就是了。

map 如果用作缓存的话，其中的元素值是“不可交换”的，通常也是“不可遗失”的（或者说对遗失敏感的）。

在涉及到本地池的 shared 字段的时候会有锁，但是这种锁是分段锁，也就是说，每个本地池都会有自己的锁。

因此，在对应某个 P 的本地池的锁处于锁定状态的时候，所有正试图访问（不论是 Get 还是 Put）这个本地池的 goroutine 都会被阻塞。

一个临时对象池拥有的本地池的数量与 P 的数量相同。所以，即使有 goroutine 因此被阻塞，往往也只是少数。又因为分段锁的缘故，它们被锁住的时间一般也是很短暂的。

当你知道了这些，你就会明白，临时对象池在并发访问方面是很高效的。

#### sync.Map

**并发安全字典**键的实际类型不能是函数类型、字典类型和切片类型。总之，我们必须保证键的类型是可比较的（或者说可判等的)。

### 线程池实现

拒绝策略：

- 一种是ch满则报错，程序主动提示报错，监控到随时扩容，防患于未然
- 一种是ch满阻塞，阻塞请求会越来越多，导致oom风险，不推荐使用。但可搭配kafka使用。

阻塞队列：

- go版本：chan本身就是一个阻塞队列，阻塞队列大小等于chan buffer

- Java版本：

- ```java
  	/**
       * 将指定元素插入队列
       *
       * @param e 待插入的对象
       */
      public void put(Object e) throws InterruptedException {
          synchronized (this) {
              while (count == items.length) {
                  // 队列已满时进入休眠
                  this.wait();
              }
  
              // 执行入队操作，将对象e实际放入队列中
              enqueue(e);
  
              // 唤醒所有休眠等待的进程
              this.notifyAll();
          }
      }
  
      /**
       * 从队列中弹出一个元素
       *
       * @return  被弹出的元素
       */
      public Object take() throws InterruptedException {
          synchronized (this) {
              while (count == 0) {
                  // 队列为空时进入休眠
                  this.wait();
              }
  
              // 执行出队操作，将队列中的第一个元素弹出
              Object e = dequeue();
  
              // 唤醒所有休眠等待的进程
              this.notifyAll();
  
              return e;
          }
      }
  ```

go  线程池实现：

```go
package fanout

import (
	"context"
	"errors"
	"runtime"
	"sync"

	xcontext "go-common/library/context"
	"go-common/library/log"
	"go-common/library/net/trace"
)

var (
	// ErrFull chan full.
	ErrFull   = errors.New("fanout: chan full")
	traceTags = []trace.Tag{
		{Key: trace.TagSpanKind, Value: "background"},
		{Key: trace.TagComponent, Value: "sync/pipeline/fanout"},
	}
)

type options struct {
	worker int
	buffer int
}

// Option fanout option
type Option func(*options)

// Worker specifies the worker of fanout
func Worker(n int) Option {
	if n <= 0 {
		panic("fanout: worker should > 0")
	}
	return func(o *options) {
		o.worker = n
	}
}

// Buffer specifies the buffer of fanout
func Buffer(n int) Option {
	if n <= 0 {
		panic("fanout: buffer should > 0")
	}
	return func(o *options) {
		o.buffer = n
	}
}

type item struct {
	f   func(c context.Context)
	ctx context.Context
}

// Fanout async consume data from chan.
type Fanout struct {
	name    string
	ch      chan *item
	options *options
	waiter  sync.WaitGroup

	ctx    context.Context
	cancel func()
}

// New new a fanout struct.
func New(name string, opts ...Option) *Fanout {
	if name == "" {
		name = "anonymous"
	}
	o := &options{
		worker: 1,
		buffer: 1024,
	}
	for _, op := range opts {
		op(o)
	}
	c := &Fanout{
		ch:      make(chan *item, o.buffer),
		name:    name,
		options: o,
	}
	c.ctx, c.cancel = context.WithCancel(context.Background())
	c.waiter.Add(o.worker)
	for i := 0; i < o.worker; i++ {
		go c.proc()
	}
	_metricChanCap.Set(float64(o.buffer), name)
	return c
}

func (c *Fanout) proc() {
	defer c.waiter.Done()
	for {
		t := <-c.ch
		if t == nil {
			return
		}
		wrapFunc(t.f)(t.ctx)
		_metricChanSize.Set(float64(len(c.ch)), c.name)
		_metricCount.Inc(c.name)
	}
}

func wrapFunc(f func(c context.Context)) (res func(context.Context)) {
	res = func(ctx context.Context) {
		defer func() {
			if r := recover(); r != nil {
				buf := make([]byte, 64*1024)
				buf = buf[:runtime.Stack(buf, false)]
				log.Errorc(ctx, "panic in fanout proc, err: %s, stack: %s", r, buf)
			}
		}()
		f(ctx)
		if tr, ok := trace.FromContext(ctx); ok {
			tr.Finish(nil)
		}
	}
	return
}

// Do save a callback func with channel full err
func (c *Fanout) Do(ctx context.Context, f func(ctx context.Context)) (err error) {
	if f == nil || c.ctx.Err() != nil {
		return c.ctx.Err()
	}
	// remove timeout
	nakeCtx := xcontext.Detach(ctx)
	if tr, ok := trace.FromContext(ctx); ok {
		tr = tr.Fork("", "Fanout:Do").SetTag(traceTags...)
		nakeCtx = trace.NewContext(nakeCtx, tr)
	}
	select {
	case c.ch <- &item{f: f, ctx: nakeCtx}:
	default:
		err = ErrFull
		_metricChanFullCount.Inc(c.name)
	}
	_metricChanSize.Set(float64(len(c.ch)), c.name)
	return
}

// SyncDo save a callback func no channel full err
func (c *Fanout) SyncDo(ctx context.Context, f func(ctx context.Context)) (err error) {
	if f == nil || c.ctx.Err() != nil {
		return c.ctx.Err()
	}
	nakeCtx := xcontext.Detach(ctx)
	if tr, ok := trace.FromContext(ctx); ok {
		tr = tr.Fork("", "Fanout:SyncDo").SetTag(traceTags...)
		nakeCtx = trace.NewContext(nakeCtx, tr)
	}
	select {
	case c.ch <- &item{f: f, ctx: nakeCtx}:
	case <-ctx.Done():
		err = ctx.Err()
	}
	_metricChanSize.Set(float64(len(c.ch)), c.name)
	return
}

// Close close fanout
func (c *Fanout) Close() error {
	if err := c.ctx.Err(); err != nil {
		return err
	}
	c.cancel()
	for i := 0; i < c.options.worker; i++ {
		c.ch <- nil
	}
	c.waiter.Wait()
	return nil
}
```

### GMP调度模型

![图片](https://gitee.com/mouri11/image/raw/master/43ffdbc6b2203d9400ac98423192caa8.png)

- CPU核心数与容器问题

```go
runtime.NumCPU()// 获取机器的CPU核心数
runtime.GOMAXPROCS(0) // 参数为零时用于获取给GOMAXPROCS设置的值
而以 Docker 为代表的容器虚拟化技术，会通过 cgroup 等技术对 CPU 资源进行隔离。这类技术对 CPU 的隔离限制，导致 runtime.NumCPU() 无法正确获取到容器被分配的 CPU 资源数。runtime.NumCPU()获取的是宿主机的核心数。
设置 GOMAXPROCS 高于真正可使用的核心数后会导致Go调度器不停地进行OS线程切换，从而给调度器增加很多不必要的工作。
目前 Go 官方并无好的方式来规避在容器里获取不到真正可使用的核心数这一问题，而 Uber 提出了一种 Workaround 方法，利用
import _ "go.uber.org/automaxprocs"
这一个包，可以在运行时根据 cgroup 为容器分配的CPU资源限制数来修改 GOMAXPROCS。包级别的 init() 函数（代码位置在 automaxpROCs/automaxpROCs.go）实现了导入这个包即可产生作用，核心函数就是 maxpROCs.Set()；这个函数会从当前的 cgroups 里获取设置的 CPU quota，然后转换为合适的 GOMAXPROCS。
```

- P的数量多少合适？

  在任何情况下，Go运行时并行执行（注意，不是并发）的 goroutines 数量是小于等于 P 的数量的。为了提高系统的性能，P 的数量肯定不是越小越好，所以官方默认值就是 CPU 的核心数。

  **设置的过小的话**，如果一个持有 P 的 M，由于 P 当前执行的 G 调用了 syscall 而导致 M 被阻塞，那么此时**关键点**：GO 的调度器是迟钝的，它很可能什么都没做，直到 M 阻塞了相当长时间以后，才会发现有一个 P/M 被 syscall 阻塞了。然后，才会用空闲的 M 来强这个 P。通过 sysmon 监控实现的抢占式调度，最快在20us，最慢在10-20ms才会发现有一个 M 持有 P 并阻塞了。操作系统在 1ms 内可以完成很多次线程调度（一般情况1ms可以完成几十次线程调度），Go 发起 IO/syscall  的时候执行该 G 的 M 会阻塞然后被OS调度走，P什么也不干，sysmon 最慢要10-20ms才能发现这个阻塞，说不定那时候阻塞已经结束了，宝贵的P资源就这么被阻塞的M浪费了。

  **P 的数量太大**，一个runtime findrunnable 时产生的损耗，另一个是线程引起的上下文切换。如果是cpu密集的业务，增加多个processor也没用，毕竟cpu计算资源就这些，来回切换反而拖慢程序。随着调多 runtime processor 数量，相关的 M 线程自然也就跟着多了起来。linux 内核为了保证可执行的线程在调度上雨露均沾，按照内核调度算法来切换就绪状态的线程，切换又引起上下文切换。上下文切换也是性能的一大杀手。findrunnable 的某些锁竞争也会触发上下文切换。

  **结论**：常规项目直接使用默认的核心数就好了，GOMAXPROCS 开太多的时候，针对计算密集型的处理性能提升反而没那么大，IO 密集(或者 syscall 较多)的 Go 程序，至少应该配置到CPU核心数目的5倍以上, 最大1024。

## 2 kratos

### BM(BladeMaster)

#### 1. 初始化 —- 配置路由 —- 启动监听，其中的核心就在 **Start** 操作中

```go
// Init http server
func Init(c *conf.Config) {
	initService(c)
	engine := bm.DefaultServer(c.BM) // 1. 初始化
	outerEngine(engine) // 2. 配置路由
	innerEngine(engine)
	if err := engine.Start(); err != nil { // 3. 启动监听
		log.Error("engine.Start error(%v)", err)
		panic(err)
	}
}
```

#### 2. engine.Start()

```go
func (engine *Engine) Start() error {
    conf := engine.conf
    // 开启监听，返回一个 listener
    l, err := net.Listen(conf.Network, conf.Addr)
    if err != nil {
        return errors.Wrapf(err, "blademaster: listen tcp: %s", conf.Addr)
    }
 
    log.Info("blademaster: start http listen addr: %s", conf.Addr)
    server := &http.Server{
        ReadTimeout:  time.Duration(conf.ReadTimeout),
        WriteTimeout: time.Duration(conf.WriteTimeout),
    }
    go func() {
         // 开一个协程，启动服务
        if err := engine.RunServer(server, l); err != nil {
            if errors.Cause(err) == http.ErrServerClosed {
                log.Info("blademaster: server closed")
                return
            }
            panic(errors.Wrapf(err, "blademaster: engine.ListenServer(%+v, %+v)", server, l))
        }
    }()
 
    return nil
}
```

- `l, err := net.Listen(conf.Network, conf.Addr)`：开启监听，返回一个 listener
- `err := engine.RunServer(server, l)`：开一个协程，启动服务（这是一个持续监听的协程，没有必要阻塞在这里）

#### 3. engine.RunServer(server, l)

```go
func (engine *Engine) RunServer(server *http.Server, l net.Listener) (err error) {
    // 可以看到engine本身也是个handler
    server.Handler = engine
    engine.server.Store(server)
    // 关键
    if err = server.Serve(l); err != nil {
        err = errors.WithStack(err)
        return
    }
    return
}
```

- `server.Handler = engine`
- `err = server.Serve(l)`：具体的监听流程

#### 4. server.Serve(l)

   ```go
   func (srv *Server) Serve(l net.Listener) error {
       // ...... 删去
    
       baseCtx := context.Background()
       if srv.BaseContext != nil {
           baseCtx = srv.BaseContext(origListener)
           if baseCtx == nil {
               panic("BaseContext returned a nil context")
           }
       }
    
       var tempDelay time.Duration // how long to sleep on accept failure
    
       ctx := context.WithValue(baseCtx, ServerContextKey, srv)
       // 注意for循环
       for {
           // 核心步骤1：accept，在这里阻塞住，等待请求
           rw, err := l.Accept()
           if err != nil {
               // ...... err暂时不看
           }
           connCtx := ctx
           if cc := srv.ConnContext; cc != nil {
               connCtx = cc(connCtx, rw)
               if connCtx == nil {
                   panic("ConnContext returned nil")
               }
           }
           tempDelay = 0
            
           c := srv.newConn(rw)
           c.setState(c.rwc, StateNew) // before Serve can return
           // 核心步骤2：serve，开一个协程去完成请求，原listener继续监听
           go c.serve(connCtx)
       }
   }
   ```
- `rw, err := l.Accept()`：监听器在端口，持续阻塞，直到有访问该端口的请求；返回请求上的连接 conn，官方来说是一个面向流的网络连接，我们可以通过这个 conn 来获取请求传来的数据
- `c := srv.newConn(rw)`：返回一个 BM 框架的 conn，之前的那个 conn 是 net.Conn
- `go c.serve(connCtx)`：BM 框架处理请求的地方，每次有请求来，都会开一个协程来处理，原 listener 会继续监听下一个请求
- **以上为获取连接请求的部分，接下来需要需要做的是处理请求，根据请求路径来做出不同的处理**

#### 5. c.serve(connCtx)

```go
1. w, err := c.readRequest(ctx)//读取 request，服务 ServeHTTP
2. serverHandler{c.server}.ServeHTTP(w, w.req)//执行 handler 的 ServeHTTP
```

#### 6. ServeHTTP(w, w.req)

```go
func (sh serverHandler) ServeHTTP(rw ResponseWriter, req *Request) {
    handler := sh.srv.Handler
    if handler == nil {
        handler = DefaultServeMux
    }
    if req.RequestURI == "*" && req.Method == "OPTIONS" {
        handler = globalOptionsHandler{}
    }
    // 注意：此处的handler为engine之前提到的engine
    handler.ServeHTTP(rw, req)
}
注意：handler 是接口，实现 ServeHTTP 方法，此处的 handler 为 engine 之前提到的 engine，在之前的 RunServer 函数中有 server.Handler = engine
```

#### 7. handler.ServeHTTP(rw, req)

```go
//接下来是 engine 的 ServeHTTP 方法
// ServeHTTP conforms to the http.Handler interface.
func (engine *Engine) ServeHTTP(w http.ResponseWriter, req *http.Request) {
    c := &Context{
        Context:  nil,
        engine:   engine,
        index:    -1,
        handlers: nil,
        Keys:     nil,
        method:   "",
        Error:    nil,
        Request:  req,
        Writer:   w,
    }
 
    engine.handleContext(c)
}
func (engine *Engine) handleContext(c *Context) {
    engine.prepareHandler(c)
    // ...
    c.Next()
}
```

#### 8. engine.prepareHandler(c) 

```go 
// 第一步，engine.prepareHandler(c)，根据 request 的 method 和 path 从 engine 的 tree（前缀树）节点中获取 handlers、params、fullPath
func (engine *Engine) prepareHandler(c *Context) {
    httpMethod := c.Request.Method
    rPath := c.Request.URL.Path
    unescape := false
    if engine.UseRawPath && len(c.Request.URL.RawPath) > 0 {
        rPath = c.Request.URL.RawPath
        unescape = engine.UnescapePathValues
    }
    rPath = cleanPath(rPath)
 
    // Find root of the tree for the given HTTP method
    // 每种方法对应一棵树
    t := engine.trees
    for i, tl := 0, len(t); i < tl; i++ {
        // 根据 method 来找到对应的树
        if t[i].method != httpMethod {
            continue
        }
        root := t[i].root
        // Find route in tree
        // 根据 path 来找到对应的节点
        value := root.getValue(rPath, c.Params, unescape)
        if value.handlers != nil {
            // 获取各个参数
            c.handlers = value.handlers
            c.Params = value.params
            c.method = httpMethod
            c.RoutePath = value.fullPath
            return
        }
        break
    }
 
    if engine.HandleMethodNotAllowed {
        for _, tree := range engine.trees {
            if tree.method == httpMethod {
                continue
            }
            if value := tree.root.getValue(rPath, nil, unescape); value.handlers != nil {
                c.handlers = engine.allNoMethod
                return
            }
        }
    }
    c.handlers = engine.allNoRoute
    return
}
```

#### 9. c.Next()

```go
// 第二步，c.Next()，执行各个 handlers，遍历执行
// Next should be used only inside middleware.
// It executes the pending handlers in the chain inside the calling handler.
// See example in godoc.
func (c *Context) Next() {
    c.index++
    for c.index < int8(len(c.handlers)) {
        // 顺讯执行handler
        c.handlers[c.index](c)
        c.index++
    }
}
```

**以上为处理请求的流程，至于 handler 的注册，tree 的构建则是在 engine 的初始化阶段，在 Start 之前**

#### 10. handler注册

```go
// 1. 封装的handler注册
// DefaultServer returns an Engine instance with the Recovery, Logger and CSRF middleware already attached.
func DefaultServer(conf *ServerConfig, options ...ServerOption) *Engine {
	engine := NewServer(conf, options...)
  // handler 注册
	engine.Use(Recovery(), Trace(), Logger(), CSRF(), Mobile(), AuroraHandler())
	if !_httpDisableRateLimit {
		engine.Use(NewRateLimiter(nil).Limit())
	}
	log.Info("blademaster: rate limit is enabled: %t", !_httpDisableRateLimit)
	return engine
}
// 2 自定义handler注册
studio := e.Group("/studio/growup/web", authSvr.User, observerSvr.OBMode())
func (group *RouterGroup) Group(relativePath string, handlers ...HandlerFunc) *RouterGroup {
	return &RouterGroup{
    // 注册
		Handlers: group.combineHandlers(handlers),
		basePath: group.calculateAbsolutePath(relativePath),
		engine:   group.engine,
		root:     false,
	}
}
```

#### 11. 路由tree构建

```go
// 1. 自定义路由
studioSwitch.GET("/incentive", incentiveSwitchOuter)
// 2. 构建tree
// GET is a shortcut for router.Handle("GET", path, handle).
func (group *RouterGroup) GET(relativePath string, handlers ...HandlerFunc) IRoutes {
	return group.handle("GET", relativePath, handlers...)
}
func (group *RouterGroup) handle(httpMethod, relativePath string, handlers ...HandlerFunc) IRoutes {
	absolutePath := group.calculateAbsolutePath(relativePath)
	injections := group.injections(relativePath)
	handlers = group.combineHandlers(injections, handlers)
	group.engine.addRoute(httpMethod, absolutePath, handlers...)
	if group.baseConfig != nil {
		group.engine.SetMethodConfig(absolutePath, group.baseConfig)
	}
	return group.returnObj()
}

func (engine *Engine) addRoute(method, path string, handlers ...HandlerFunc) {
	if path[0] != '/' {
		panic("blademaster: path must begin with '/'")
	}
	if method == "" {
		panic("blademaster: HTTP method can not be empty")
	}
	if len(handlers) == 0 {
		panic("blademaster: there must be at least one handler")
	}
	if _, ok := engine.metastore[path]; !ok {
		engine.metastore[path] = make(map[string]interface{})
	}
	engine.metastore[path]["method"] = method

	root := engine.trees.get(method)
	if root == nil {
		root = new(node)
    // 构建路由tree
		engine.trees = append(engine.trees, methodTree{method: method, root: root})
	}
	root.addRoute(path, handlers)
}
```

### 3 Redis

#### 使用

```go
// 1. 第一步 New 一个 RedisBlockingLock（几乎等同于 RedisLock），加了一堆参数，按照惯例来说应该是给 redisLock 赋值初始化的
redisLock := lock.NewRedisBlockingLock(s.dao.Redis(), fmt.Sprintf("%s:gen:lock", grantID), lock.BlockingSleep(time.Millisecond*10), lock.BlockingTime(time.Second))
// 2. 第二步执行 Lock()
if ok, err = redisLock.Lock(ctx); err != nil {
    err = errors.WithStack(err)
    return
}
if !ok {
    return
}
// 3. 第三步执行 Unlock()
defer redisLock.Unlock(ctx)



lockOpts := combine(opts...)简单来说就把传进来的那些参数赋值到 lockOpts 中
store：redis 连接
name：在之后担任 redis 的 key 的任务
token：随机生成，在之后担任 redis 的 value 的任务
opts：包括了上锁的时间等参数

func NewRedisBlockingLock(store *redis.Redis, name string, opts ...Option) Locker {
    lockOpts := combine(opts...)
    return &redisBlockingLock{
        redisLock: &redisLock{
            store: store,
            name:  name,
            token: uuid.NewV4().String(),
            opts:  lockOpts,
        },
    }
}


// 接着是第二步 Lock()，RedisBlockingLock 的 Lock 其实就是调用 RedisLock 的 Lock，如果失败就 sleep 一段时间后重试
func (r *redisBlockingLock) Lock(ctx context.Context) (bool, error) {
    ok, err := r.redisLock.Lock(ctx)
    if err != nil || ok {
        return ok, err
    }
    deadline := time.Now().Add(r.opts.blockingTime)
    // try lock resource util the deadline
    for deadline.Sub(time.Now()) > 0 {
        select {
        case <-time.After(r.opts.blockingSleep):
            ok, err = r.redisLock.Lock(ctx)
            if err != nil || ok {
                return ok, err
            }
        case <-ctx.Done():
            err = errors.Wrapf(ctx.Err(), "failed to lock %s because the context is closed", r.name)
            return false, err
        }
    }
    return false, nil
}

redis 的 EVAL 命令执行 lua 脚本
脚本中的第一步：setnx（SET if Not eXists）命令在指定的 key 不存在时，为 key 设置指定的值，设置成功，返回 1；设置失败，返回 0
脚本中的第二步：pexpire，与 expire 相同，只是时间的单位变成毫秒
逻辑就是以 RedisLock 的 name 为 key，以 token 为 value，以 opts.timeoutInMs 为 expire time 存储一条信息，如果该条信息存在，表示该 name 已经上了锁
func (r *redisLock) Lock(ctx context.Context) (bool, error) {
    //KEYS[1] - lock name
    //ARGV[1] - token
    //ARGV[2] - timeout in milliseconds
    //return 1 if lock was acquired, otherwise 0
    script := `if redis.call('setnx', KEYS[1], ARGV[1]) == 1 then
            if ARGV[2] ~= '' then
                redis.call('pexpire', KEYS[1], ARGV[2])
            end
            return 1
        end
        return 0`
    reply, err := r.store.Do(ctx, "EVAL", script, 1, r.name, r.token, r.opts.timeoutInMs)
    if err != nil {
        return false, errors.Wrapf(err, "failed to lock %s during executing lua script", r.name)
    }
    return reply.(int64) == 1, nil
}

// 第三步 Unlock()
// redis 的 EVAL 命令执行 lua 脚本
// del 命令删除 name 对应的信息
// 逻辑就是如果 name 对应的 token 不存在或者 token 错误，则返回 0；不然就删除该条数据，返回 1
func (r *redisLock) Unlock(ctx context.Context) error {
    // KEYS[1] - lock name
    // ARGS[1] - token
    // return 1 if the lock was released, otherwise 0
    script := `local token = redis.call('get', KEYS[1])
        if not token or token ~= ARGV[1] then
            return 0
        end
        redis.call('del', KEYS[1])
        return 1`
    reply, err := r.store.Do(ctx, "EVAL", script, 1, r.name, r.token)
    if err != nil {
        return errors.Wrapf(err, "failed to unlock %s during executing lua script", r.name)
    }
    if reply.(int64) != 1 {
        return errors.Wrapf(err, "failed to unlock %s because lock not longer owned", r.name)
    }
    return nil
}
```

**综上 RedisLock 为了实现锁的功能，加锁本质上就是在 redis 中存一条数据，如果该数据不存在则成功取锁，存在返回**

综上 RedisLock 为了实现锁的功能，加锁本质上就是在 redis 中存一条数据，如果该数据不存在则成功取锁，存在返回

有几个注意点：

问：为什么需要用 lua 脚本来执行逻辑呢？

答：为了保证逻辑的原子性，因为 redis 处理每个请求是**单线程**执行的，在执行 lua 脚本时，其他请求必须等待，直到 lua 脚本处理完成。比如 unlock 操作的逻辑是 get + del，如果不是原子性操作，在 get 和 del 之间可能会有别人的 set 操作，导致将别人的锁删除

问：为什么需要 token？

答：保证 unlock 的时候这个锁是自己的，不要误删别人的锁。因为有 ttl 的存在，如果程序运行时间过长导致 ttl 过期，之后你又来 unlock，这可能会删掉别人的锁，因此需要 token 来判断一下

问：如果 ttl 过期了而程序还没执行完怎么办？

答：似乎也许好像没有办法。其实是有办法的，但是似乎也许好像没有实现。我们可以开启一个**守护线程**来监控，如果程序没有结束，当 ttl 快要到的时候就自动续期。具体可参考：Redisson

#### 如何保证缓存一致性

旁路缓存策略+redis的setNX和setEX

- 读缓存不存在，从数据库更新数据到缓存时，用setNX
- 写库更新缓存时，用setEX

![wecom-temp-a38c45385124749879949e299d933907](https://gitee.com/mouri11/image/raw/master/wecom-temp-a38c45385124749879949e299d933907.png)

#### 缓存击穿的变种问题

问：有一个高qps的接口，需要处理一个计算量很重的任务（比如操作db）,现在所有的请求都依赖这个相同的任务，如何保证系统性能又能使这个任务仅计算一次？

答：本质是缓存击穿的变种，可以采用分布式锁+缓存热点数据解决。

### 4 Audit 基础服务

**原理：**

借助es倒排索引的思想，根据audit_index表中的配置的索引字段，借助于binlog日志同步索引变更到es，实现了快速从索引字段到mid，根据mid查找audit_info表数据的目的。

本质：使用es来快速索引extra大字符串的目的。

**方案选型：**

es实现 Vs mysql自带的 full text index

- innodb不支持全文检索，myisam不支持事务，audit需要auditInfo和auditlog事务处理

- 全文检索不支持范围查找

**隐患：**

- audit binlog同步 es延迟问题，生产上出现10s延迟。在高QPS的情况下，由于binlog变更更频繁，长延迟不可避免。本质的问题是，es充当了一个伪从库dump binlog日志，但是延迟时长往往高于一般的从库，使用场景是一种读从库，写主库的方式，读延迟难以避免。
- 由于读延迟的架构问题，不太适合ToC。比较适合小活动，快速上线。

### 5 规范设计

非编码类规范和编码类规范：

- 非编码类规范，主要包括开源规范、文档规范、版本规范、Commit 规范和发布规范。
- 编码类规范，则主要包括目录规范、代码规范、接口规范、日志规范和错误码规范。

#### 5.1 commit 规范

在这些规范中，Angular 规范在功能上能够满足开发者 commit 需求，在格式上清晰易读，目前也是用得最多的。Angular 规范其实是一种语义化的提交规范（Semantic Commit Messages）。

在 Angular 规范中，Commit Message 包含三个部分，分别是 Header、Body 和 Footer，格式如下：

```go
<type>[optional scope]: <description>
// 空行
[optional body]
// 空行
[optional footer(s)]
```

其中，Header 是必需的，Body 和 Footer 可以省略。在以上规范中，必须用括号 () 括起来， [] 后必须紧跟冒号 ，冒号后必须紧跟空格，2 个空行也是必需的。可以基于这些 Commit Message 进行过滤查找，比如只查找某个版本新增的功能：git log --oneline --grep "^feat|^fix|^perf"。

Header 部分只有一行，包括三个字段：type（必选）、scope（可选）和 subject（必选）。我们先来说 type，它用来说明 commit 的类型。为了方便记忆，我把这些类型做了归纳，它们主要可以归为 Development 和 Production 共两类。

![img](https://gitee.com/mouri11/image/raw/master/89c618a7415c0c38b09d86d7f882a427.png)

subject 是 commit 的简短描述，必须以动词开头、使用现在时。比如，我们可以用 change，却不能用 changed 或 changes，而且这个动词的第一个字母必须是小写。通过这个动词，我们可以明确地知道 commit 所执行的操作。此外我们还要注意，subject 的结尾不能加英文句号。

但是除了 Commit Message 规范之外，在代码提交时，我们还需要关注 3 个重点内容：提交频率、合并提交和 Commit Message 修改。

**合并提交：**

在 Git 中，我们主要使用 git rebase 命令来合并。git rebase 也是我们日后开发需要经常使用的一个命令，所以我们一定要掌握好它的使用方法。`git rebase -i <父commit id>`这里的一定要是需要合并 commit 中最旧 commit 的父 commit ID。

这里有两点需要你注意：

- Commit Message 是 commit 数据结构中的一个属性，如果 Commit Message 有变更，则 commit ID 一定会变，git commit --amend 只会变更最近一次的 commit ID，但是 git rebase -i 会变更父 commit ID 之后所有提交的 commit ID。
- 如果当前分支有未 commit 的代码，需要先执行 git stash 将工作状态进行暂存，当修改完成后再执行 git stash pop 恢复之前的工作状态。

#### 5.2 Git 开发模式

在使用 Git 开发时，有 4 种常用的工作流，也叫开发模式，按演进顺序分为集中式工作流、功能分支工作流、Git Flow 工作流和 Forking 工作流。

Git Flow 工作流：

Git Flow 中定义了 5 种分支，分别是 master、develop、feature、release 和 hotfix。其中，master 和 develop 为常驻分支，其他为非常驻分支，不同的研发阶段会用到不同的分支。这 5 种分支的详细介绍见下表：

![img](https://gitee.com/mouri11/image/raw/master/fa611f83053afd77cf3ddf83561ba1d9.png)
