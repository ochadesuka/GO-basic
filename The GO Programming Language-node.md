# golang

### 变量

也可以在一个声明语句中同时声明一组变量，或用一组初始化表达式声明并初始化一组变
量。如果省略每个变量的类型，将可以声明多个类型不同的变量（类型由初始化表达式推
导）：

```
var i, j, k int // int, int, int
var b, f, s = true, 2.3, "four" // bool, float64,string
```

简短变量：

```
anim := gif.GIF{LoopCount: nframes}
freq := rand.Float64() * 3.0
t := 0.0
```

简短变量声明被广泛用于大部分的局部变量的声明和初始化。var形式的声明语句往往是用于需要显式指定变量类型地方，

或者因为变量稍后会被重新赋值而初始值无关紧要的地方。

简短变量声明语句中必须至少要声明一个新的变量。

变量交换：

```
i, j = j, i // 交换 i 和 j 的值
```

指针：

一个指针的值是另一个变量的地址。一个指针对应变量在内存中的存储位置。

一般 *p 表达式读取指针指向的变量的值。

对于聚合类型每个成员——比如结构体的每个字段、或者是数组的每个元素——也都是对应一个变量，因此可以被取地址。

任何类型的指针的零值都是nil。指针之间也是可以进行相等测试的，只有当它们指向同一个变量或全部是nil时才相等。

每次调用new函数都是返回一个新的变量的地址，因此下面两个地址是不同的：

```
p := new(int)
q := new(int)
fmt.Println(p == q) // "false"
```

一个变量的有效周期只取决于是否可达，因此一个循环迭代内部的局部变量的生命周期可能超出其局部作用域。同时，局部变量可能在函数返回之后依然存在。编译器会自动选择在栈上还是在堆上分配局部变量的存储空间:

```
var global *int
func f() {
var x int
x = 1
global = &x
}
func g() {
y := new(int)
*y = 1
}
f
```

f函数里的x变量必须在堆上分配，因为它在函数退出后依然可以通过包一级的global变量找到，虽然它是在函数内部定义的，用Go语言的术语说，这个x局部变量从函数f中逃逸了。当g函数返回时，变量 *y 将是不可达的，也就是说可以马上被回收的。因此， *y 并没有从函数g中逃逸，编译器可以选择在栈上分配 *y 的存储空间。逃逸的变量需要额外分配内存，同时对性能的优化可能会产生细微的影响。

赋值：

元组赋值是另一种形式的赋值语句，它允许同时更新多个变量的值。在赋值之前，赋值语句右边的所有表达式将会先进行求值，然后再统一更新左边对应变量的值。比如上述的交换。

**包**

init初始化：

```
func init() { /* ... */ }
```

这样的init初始化函数除了不能被调用或引用外，其他行为和普通函数类似。在每个文件中的

init初始化函数，在程序开始执行时按照它们声明的顺序被自动调用。

如果一个p包导入了q包，那么在p包初始化的时候可以认为q包必然已经初始化过了。初始化

工作是自下而上进行的，main包最后被初始化。以这种方式，可以确保在main函数执行之

前，所有依赖的包都已经完成初始化工作了。

使用匿名函数进行复杂初始化：

```
// pc[i] is the population count of i.
var pc [256]byte = func() (pc [256]byte) {
for i := range pc {
pc[i] = pc[i/2] + byte(i&1)
}
return
}()
```

**字符串：**

字符串的值是不可变的：一个字符串包含的字节序列永远不会被改变，当然我们也可以给一个字符串变量分配一个新字符串值。尝试修改字符串内部数据的操作也是被禁止的：

```
s[0] = 'L' // compile error: cannot assign to s[0]
```



**常量：**

常量生成器：

常量声明可以使用iota常量生成器初始化，它用于生成一组以相似规则初始化的常量，在一个const声明语句中，在第一个声明的常量所在的行，

iota将会被置为0，然后在每一个有常量声明的行加一。

```
const (
Sunday Weekday = iota
Monday
Tuesday
Wednesday
Thursday
Friday
Saturday
)
```



**数组：**

在数组字面值中，如果在数组的长度位置出现的是“...”省略号，则表示数组的长度是根据初始化值的个数来计算。因此，上面q数组的定义可以简化为

```
q := [...]int{1, 2, 3}
fmt.Printf("%T\n", q) // "[3]int"
```

未指定初始值的元素将用零值初始化：

```
r := [...]int{99: -1}
```

定义了一个含有100个元素的数组r，最后一个元素被初始化为-1，其它元素都是用0初始化。

如果一个数组的元素类型是可以相互比较的，那么数组类型也是可以相互比较的，这时候我们可以直接通过==比较运算符来比较两个数组，只有当两个数组的所有元素都是相等的时候数组才是相等的。不相等比较运算符!=遵循同样的规则。



**Slice:**

Slice（切片）代表变长的序列，序列中每个元素都有相同的类型。一个slice类型一般写作[]T

一个slice由三个部分构成：指针、长度和容量。指针指向第一个slice元素对应的底层数组元素的地址.

slice底层还是引用的数组对象，并且可以修改底层数组的元素，复制slice只是对底层数组建了个别名。

slice与数组初始化很相似，但是slice没有指定长度：

```
s := []int{0, 1, 2, 3, 4, 5}
```

slice之间不能用==比较，但是可以s == nil

```
var s []int // len(s) == 0, s == nil
s = nil // len(s) == 0, s == nil
s = []int(nil) // len(s) == 0, s == nil
s = []int{} // len(s) == 0, s != nil
```



**Map: map[K]V**

当map中不存在元素时，通过map[key]获取元素或返回v的初始值。（比如当v为int时，获取一个不存在的key的值，会返回0，要区分0是空值还是key的值）

对map的元素不能取址，因为map的大小是动态变化的，有可能会为map重新分配更大的空间，之前的地址就会无效。

遍历map时，顺序是随机的，每次都使用随机的遍历顺序可以强制要求程序不会依赖具体的哈希函数实现。



**结构体：**

一个命名为S的结构体类型将不能再包含S类型的成员：因为一个聚合的值不能包含它自身。（该限制同样适用于数组。）但是S类型的结构体可以包含*S指针类型的成员，这可以让我们创建递归的数据结构。

在函数中结构体作为参数用指针传递效率更好，而且在Go语言中，所有的函数参数都是值拷贝传入的，函数参数将不再是函数调用时的原始变量。

如果结构体的全部成员都是可以比较的，那么结构体也是可以比较的，那样的话两个结构体将可以使用==或!=运算符进行比较。

只声明一个成员对应的数据类型而不指名成员的名字；这类成员就叫匿名成员。

```
type Point struct {
    X, Y int
}
type Circle struct {
    Point
    Radius int
}
type Wheel struct {
    Circle
    Spokes int
}
var w Wheel
w.X = 8            // equivalent to w.Circle.Point.X = 8
w.Y = 8            // equivalent to w.Circle.Point.Y = 8
w.Radius = 5       // equivalent to w.Circle.Radius = 5
w.Spokes = 20
w = Wheel{8, 8, 5, 20}                       // compile error: unknown fields
w = Wheel{X: 8, Y: 8, Radius: 5, Spokes: 20} // compile error: unknown fields
w = Wheel{Circle{Point{8, 8}, 5}, 20}

w = Wheel{
    Circle: Circle{
        Point:  Point{X: 8, Y: 8},
        Radius: 5,
    },
    Spokes: 20, // NOTE: trailing comma necessary here (and at Radius)
}
```

不能同时包含两个类型相同的匿名成员，这会导致名字冲突。

```
type Movie struct {
    Title  string
    Year   int  `json:"released"`
    Color  bool `json:"color,omitempty"`
    Actors []string
}
```

Year，Color后面的字符串为成员的tag，json开头键名对应的值用于控制encoding/json包的编码和解码的行为，并且encoding/...下面其它的包也遵循这个约定。omitempty表示当该成员为空或者零值时不生成该json对象。

**文本和html模板**



**递归：**

大部分编程语言使用固定大小的函数调用栈，常见的大小从64KB到2MB不等。固定大小栈会限制递归的深度，当你用递归处理大量数据时，需要避免栈溢出；除此之外，还会导致安全性问题。与此相反，Go语言使用可变栈，栈的大小按需增加(初始时很小)。这使得我们使用递归时不必考虑溢出和安全问题。

我们必须确保resp.Body被关闭，释放网络资源。虽然Go的垃圾回收机制会回收不被使用的内存，但是这不包括操作系统层面的资源，比如打开的文件、网络连接。因此我们必须显式的释放这些资源。

如果一个函数所有的返回值都有显式的变量名，那么该函数的return语句可以省略操作数。这称之为bare return。（不建议使用，影响代码阅读）

```
func CountWordsAndImages(url string) (words, images int, err error) {
    resp, err := http.Get(url)
    if err != nil {
        return
    }
    doc, err := html.Parse(resp.Body)
    resp.Body.Close()
    if err != nil {
        err = fmt.Errorf("parsing HTML: %s", err)
        return
    }
    words, images = countWordsAndImages(doc)
    return
}
```



**错误处理策略：**

1.传播错误：这意味着函数中某个子程序的失败，会变成该函数的失败。

```
resp, err := http.Get(url)
if err != nil{
    return nil, err
}
```

2.重试机制：

```
func WaitForServer(url string) error {
    const timeout = 1 * time.Minute
    deadline := time.Now().Add(timeout)
    for tries := 0; time.Now().Before(deadline); tries++ {
        _, err := http.Head(url)
        if err == nil {
            return nil // success
        }
        log.Printf("server not responding (%s);retrying…", err)
        time.Sleep(time.Second << uint(tries)) // exponential back-off
    }
    return fmt.Errorf("server %s failed to respond after %s", url, timeout)
}
```

3.输出保存错误信息，中断程序：这种策略只应在main中执行。对库函数而言，应仅向上传播错误，除非该错误意味着程序内部包含不一致性，即遇到了bug，才能在库函数中结束程序。

4.只输出错误信息不中断程序：

```
if err := Ping(); err != nil {
    log.Printf("ping failed: %v; networking disabled",err)
}
```

5.忽略错误

**函数值：**

函数被看作第一类值（first-class values）：函数像其他值一样，拥有类型，可以被赋值给其他变量，传递给函数，从函数返回。对函数值（function value）的调用类似函数调用。零值为nil，但是函数值之间是不可比较的，也不能用函数值作为map的key。

**可变参数：**

在声明可变参数函数时，需要在参数列表的最后一个参数类型之前加上省略符号“...”，这表示该函数会接收任意数量的该类型参数。

```
func sum(vals...int) int {
    total := 0
    for _, val := range vals {
        total += val
    }
    return total
}
fmt.Println(sum(1, 2, 3, 4)) // "10"
values := []int{1, 2, 3, 4}
fmt.Println(sum(values...)) // "10"
```



**defer：**

只需要在调用普通函数或方法前加上关键字defer，就完成了defer所需要的语法。当执行到该条语句时，函数和参数表达式得到计算，但直到包含该defer语句的函数执行完毕时，defer后的函数才会被执行，不论包含defer语句的函数是通过return正常结束，还是由于panic导致的异常结束。你可以在一个函数中执行多条defer语句，它们的执行顺序与声明顺序相反。defer语句经常被用于处理成对的操作，如打开、关闭、连接、断开连接、加锁、释放锁。



**Recover捕获异常：**

如果在deferred函数中调用了内置函数recover，并且定义该defer语句的函数发生了panic异常，recover会使程序从panic中恢复，并返回panic value。导致panic异常的函数不会继续运行，但能正常返回。在未发生panic时调用recover，recover会返回nil。

```
func Parse(input string) (s *Syntax, err error) {
    defer func() {
        if p := recover(); p != nil {
            err = fmt.Errorf("internal error: %v", p)
        }
    }()
    // ...parser...
}
```

安全的做法是有选择性的recover。换句话说，只恢复应该被恢复的panic异常，此外，这些异常所占的比例应该尽可能的低。为了标识某个panic是否应该被恢复，我们可以将panic value设置成特殊类型。在recover时对panic value进行检查，如果发现panic value是特殊类型，就将这个panic作为errror处理，如果不是，则按照正常的panic进行处理。

```
func soleTitle(doc *html.Node) (title string, err error) {
    type bailout struct{}
    defer func() {
        switch p := recover(); p {
        case nil:       // no panic
        case bailout{}: // "expected" panic
            err = fmt.Errorf("multiple title elements")
        default:
            panic(p) // unexpected panic; carry on panicking
        }
    }()
```



**方法：**

声明：

```
func (p Point) Distance(q Point) float64 {
    return math.Hypot(q.X-p.X, q.Y-p.Y)
}
func (p *Point) ScaleBy(factor float64) {
    p.X *= factor
    p.Y *= factor
}
```

附加的参数p，叫做方法的接收器(receiver)，这个接受者变量本身比较大时，我们就可以用其指针而不是对象来声明方法。当用指针声明方法时，方法名为(*Point).ScaleBy，在调用得时候，通常go会隐式声明指针变量，当p为Point的指针时，p.ScaleBy(factor)也是可以的。

不论接收器的实际参数和其形式参数是相同，比如两者都是类型T或者都是类型*T：

```
Point{1, 2}.Distance(q) //  Point
pptr.ScaleBy(2)         // *Point
```

或者接收器实参是类型T，但接收器形参是类型*T，这种情况下编译器会隐式地为我们取变量的地址：

```
p.ScaleBy(2) // implicit (&p)
```

或者接收器实参是类型*T，形参是类型T。编译器会隐式地为我们解引用，取到指针指向的实际变量：

```
pptr.Distance(q) // implicit (*pptr)
```



当结构体内嵌后，外部的类型可以调用内嵌类型的方法：

```
type ColoredPoint struct {
    Point
    Color color.RGBA
}
var p = ColoredPoint{Point{1, 1}, red}
var q = ColoredPoint{Point{5, 4}, blue}
fmt.Println(p.Distance(q.Point)) // "5"
p.ScaleBy(2)
q.ScaleBy(2)
```



**bit数组：**

一个bit数组通常会用一个无符号数或者称之为“字”的slice来表示，每一个元素的每一位都表示集合里的一个值。

**封装：**

封装主要依赖于首字母大小写，大写可以导出到其他包，小写只能在当前包内访问。

封装提供了三方面的优点。

首先，因为调用方不能直接修改对象的变量值，其只需要关注少量的语句并且只要弄懂少量变量的可能的值即可。

第二，隐藏实现的细节，可以防止调用方依赖那些可能变化的具体实现，这样使设计包的程序员在不破坏对外的api情况下能得到更大的自由。

第三个，是阻止了外部调用方对对象内部的值任意地进行修改。



**接口：**

接口类型具体描述了一系列方法的集合，一个实现了这些方法的具体类型是这个接口类型的实例。

io.Writer类型是用的最广泛的接口之一，因为它提供了所有的类型写入bytes的抽象，包括文件类型，内存缓冲区，网络链接，HTTP客户端，压缩工具，哈希等等

一个类型如果拥有一个接口需要的所有方法，那么这个类型就实现了这个接口。

接口指定的规则非常简单：表达一个类型属于某个接口只要这个类型实现这个接口。

```
var w io.Writer
w = os.Stdout // OK: *os.File has Write method
w = new(bytes.Buffer) // OK: *bytes.Buffer has Write method
w = time.Second // compile error: time.Duration lacks Write method
var rwc io.ReadWriteCloser
rwc = os.Stdout // OK: *os.File has Read, Write, Close methods
rwc = new(bytes.Buffer) // compile error: *bytes.Buffer lacks Close method
w = rwc // OK: io.ReadWriteCloser has Write method
rwc = w // compile error: io.Writer lacks Close method
```

对于创建的一个interface{}值持有一个boolean，float，string，map，pointer，或者任意其它的类型；我们当然不能直接对它持有的值做操作，因为interface{}没有任何方法。可以使用类型断言。

坑：一个包含空指针值的非空接口，会出现空指针panic

```
func main() {
    var buf *bytes.Buffer //var buf io.Writer
    if debug {
        buf = new(bytes.Buffer) // enable collection of output
    }
    f(buf) // NOTE: subtly incorrect!
    if debug {
        // ...use buf...
    }
}

// If out is non-nil, output will be written to it.
func f(out io.Writer) {
    // ...do something...
    if out != nil {
        out.Write([]byte("done!\n"))
    }
}
```

当debug为false时，out传入了一个*bytes.Buffer类型的空值，在f函数中，if语句判断时，out ！= nil是为true的，但是在调用out.Write的时候，会发生空指针panic。解决方法为：将buf的类型定义为io.Writer



例：sort.interface接口

go中要实现排序，不需要指定容器的具体类型，但是序列需要定义Len()(获取序列长度)、Less方法(比较两个元素的接口)、Swap方法(交换元素)。

```
type StringSlice []string
func (p StringSlice) Len() int           { return len(p) }
func (p StringSlice) Less(i, j int) bool { return p[i] < p[j] }
func (p StringSlice) Swap(i, j int)      { p[i], p[j] = p[j], p[i] }
sort.Sort(StringSlice(names))
```

接口赋值：

如果接口A的方法列表是接口B的方法列表的子集，那么接口B可以赋值给接口A。

接口查询：

想要接口A赋值给接口B，需要用到接口查询：

```
type Writer interface {
    Write(buf []byte) (n int, err error)
}
type IStream interface {
    Write(buf []byte) (n int, err error)
    Read(buf []byte) (n int, err error)
}
var file1 Writer = ...
if file5, ok := file1.(IStream); ok {
...
}
```

这个if语句检查file1接口指向的对象实例是否实现了two.IStream接口，如果实现了，则执行特定的代码。

接口查询特例：查询对象是否是某个类型：

```
var file1 Writer = ...
if file6, ok := file1.(*File); ok {
...
}
```



接口组合：

```
type ReadWriter interface {
    Read(p []byte) (n int, err error)
    Write(p []byte) (n int, err error)
}
```



**http.Hanlder:**

ServeMux：

net/http包提供了一个请求多路器ServeMux来简化URL和handlers的联系。

```
func main() {
    db := database{"shoes": 50, "socks": 5}
    mux := http.NewServeMux()
    mux.Handle("/list", http.HandlerFunc(db.list))
    mux.Handle("/price", http.HandlerFunc(db.price))
    log.Fatal(http.ListenAndServe("localhost:8000", mux))
}
```



error:

```
type error interface {
    Error() string
}
```

新建一个error：

```
errors.New(text)
```



**类型断言：**

x.(T)，x表示一个接口的类型和T表示一个类型。一个类型断言检查它操作对象的动态类型是否和断言的类型匹配。

**goroutine & channel**

goroutine是一种函数的并发执行方式，而channel是用来在goroutine之间进行参数传递。

main中调用gorountine，主线程不会等待协程结束再结束，当主线程结束时，协程可能还没运行就结束了。

“不要通过共享内存来通信，而应该通过通信来共享内存。”

我们可以使用channel在两个或

多个goroutine之间传递消息。channel是进程内的通信方式，因此通过channel传递对象的过程和调用函数时的参数传递行为比较一致，比如也可以传递指针等。

一个channel只能传递一种类型的值，这个类型需要在声明channel时指定。

```
package main
import "fmt"
func Count(ch chan int) {
    ch <- 1
    fmt.Println("Counting")
}
func main() {
    chs := make([]chan int， 10)
    for i := 0; i < 10; i++ {
        chs[i] = make(chan int)
        go Count(chs[i])
    }
    for _, ch := range(chs) {
        <-ch
    }
}
```

向channel写入数据通常会导致程序阻塞，直到有其他goroutine从这个channel中读取数据。

Go语言直接在语言级别支持select关键字，用于处理异步IO问题。select的用法与switch语言非常类似，但每个case语句里必须是一个IO操作。

```
select {
case <-chan1:
// 如果chan1成功读到数据，则进行该case处理语句
case chan2 <- 1:
// 如果成功向chan2写入数据，则进行该case处理语句
default:
// 如果上面都没有成功，则进入default处理流程
}
```

**socket编程：**

func Dial(net, addr string) (Conn, error)

TCP链接：

conn, err := net.Dial("tcp", "192.168.0.10:2100")

UDP链接：

conn, err := net.Dial("udp", "192.168.0.12:975")

ICMP链接（使用协议名称）：

conn, err := net.Dial("ip4:icmp", "www.baidu.com")

ICMP链接（使用协议编号）：

conn, err := net.Dial("ip4:1", "10.0.0.3")

在成功建立连接后，我们就可以进行数据的发送和接收。发送数据时，使用conn的Write()

成员方法，接收数据时使用Read()方法。

**异常捕获recover:**

当程序遇到panic错误时会立即宕机，但很多时候我们需要遇到错误也还是要保证程序正常运行。比如一个项目中有多个协程，如果某个协程遇到panic，会导致整个程序停止。

但我们实际上需要其他协程也正常运行，在java中可以通过try/catch捕获异常，go中则用到了recover。

recover必须在defer后面使用，示例：

```
func Run() {
   defer func() {
      if err := recover(); err != nil {
         gwlog.Error("panic error: %v", err)
      }
   }()

   router.RegisterRouter()
   //启动服务，监听端口
   err := http.ListenAndServe(":8080", nil)
   if err != nil {
      panic(err)
   }
}
```

recover只能捕获本协程内的panic，语言级别的panic错误无法捕获，程序会直接崩溃，比如死锁、数据竞争。