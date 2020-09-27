### Closures：闭包

一个函数以及它所捕获的外部的常量或者变量，组合起来，称之为**闭包**。Swift中的闭包类似于C和Objective-C中的`block`。

闭包可以看成是一个**类的实例**，内存是在**堆空间**，捕获的**常量**或者**变量**可以看作是类的属性，函数可以看成是类的实例方法。

下面是闭包和类的对比，便于记忆

```swift
typealias plusCloseure = (Int) -> Int

func plusAction() -> plusCloseure {
    var num = 0
    
    func plus(_ count:Int) -> Int{
        num = num + count
        return num
    }
    return plus
}

var plus1 = plusAction()
var plus2 = plusAction()
    
withUnsafePointer(to: &plus1) {ptr in print(ptr)}
withUnsafePointer(to: &plus2) {ptr in print(ptr)}
    
//print("plus1 is \(plus1) plus2 is \(plus2)")
    
print(plus1(1))
print(plus2(2))
print(plus1(3))
print(plus2(4))
```

```swift
class plusClass {
    var num = 0
    func plus(_ count: Int) -> Int {
        num = num + count
        return num
    }
}
let plus1 = plusClass()
let plus2 = plusClass()
print(plus1.plus(1))
print(plus2.plus(2))
print(plus1.plus(3))
print(plus2.plus(4))
```





#### 闭包表达式

swift中可以通过<font color=red>func</font>来定义一个函数，也可以通过<font color=red>闭包表达式</font>来定义一个函数

**func的方法**

```swift
func sum(_ num1: Int,_ num2: Int) -> Int {
    return num1 + num2
}
```

**闭包表达式的方法**

```swift
var sumClosure = {
    (num1: Int,num2: Int) -> Int in
    return num1 + num2
}
```

**闭包表达式语法**

闭包表达式语法具有以下一般形式：

```swift
{ (<#parameters#>) -> <#returnType#> in
    <#statements#> 
}
```



**排序方法**

下方通过排序方法介绍一下闭包是如何一步步省略部分关键字的

`sorted（by :)`方法接受一个闭包，该闭包接受与数组中元素相同类型的两个参数，并返回一个`Bool`值，若返回是`true`说明第一个值是出现在第二个值之前。若返回是`false`说明第一个值是出现在第二个值之后。

```swift
//排序数组 - func
let names = ["kobe", "lebron", "allen", "tim", "yao"]
//定义递减的排序函数
func descendingFunc(_ s1: String, _ s2: String) -> Bool {
    return s1 > s2//表示降序
}
let newArray = names.sorted(by: descendingFunc)
print(newArray)//["yao", "tim", "lebron", "kobe", "allen"]

```

闭包表达式语法中的参数可以是输入输出参数，但不能具有默认值。上述示例使用闭包表达式语法

```swift
//排序数组 - 闭包表达式
let newArray = names.sorted(by: {(s1:String,s2:String)->Bool in
            return s1 > s2
})
print(newArray)//!< ["yao", "tim", "lebron", "kobe", "allen"]
```



对于内联闭包表达式，参数和返回类型写在花括号内，而不是在花括号外。闭包的方法体的开头由`in`关键字引入。这个关键字表示闭包的参数和返回类型的定义已经完成，闭包的方法体即将开始。



**从上下文中推断类型**

因为排序闭包(函数类型)作为参数传递给方法，所以Swift可以推断出它的参数类型以及它返回的值的类型。这意味着可以省略`- >`，返回值类型和参数名称周围的括号：

```swift
let names = ["kobe", "lebron", "allen", "tim", "yao"]
let newArray = names.sorted(by: {s1,s2 in return s1 > s2})

//也可以这样写
names.sorted { (s1, s2) -> Bool in
    return s1 > s2
}
//也可以这么写
names.sorted(by: {s1,s2 in
            return s1 > s2
        })
print(newArray)//!< ["yao", "tim", "lebron", "kobe", "allen"]
print(names)//!<["kobe", "lebron", "allen", "tim", "yao"]

```

还可以省略关键字`return`，故上述示例也可写为：

```swift
let names = ["kobe", "lebron", "allen", "tim", "yao"]
let newArray = names.sorted(by: {s1,s2 in s1 > s2})

```



**简写参数名称**

简写参数名称，可通过名称`$0`，`$1`，`$2`等引用闭包参数的值。`in`关键字也可以省略。

```swift
let names = ["kobe", "lebron", "allen", "tim", "yao"]
let newArray = names.sorted(by: {$0 > $1})

```





#### 尾随闭包

如果闭包作为函数的最后一个参数，则可以使用尾随闭包

```swift
//尾随闭包
func trailingClosure() {
    
    //闭包表达式作为函数的最后一个函数
    func trailingClosures(paramter: String,block:(_:String) -> ()) {
        block(paramter + "：trailingClosures")
    }

    //可以有非尾随闭包的调用形式
    func blockFunc(_ paramter:String) {
        print(paramter)
    }

    trailingClosures(paramter: "不是尾随闭包写法1", block: blockFunc)
    trailingClosures(paramter: "不是尾随闭包写法2", block: {(parameter:String) in print(parameter)})
    trailingClosures(paramter: "不是尾随闭包写法3", block: {(parameter)in print(parameter)})//参数类型和返回值可以根据函数类型得知，故可以省略

    //尾随闭包写法的调用形式，block函数标签不能出现在圆括号内
    trailingClosures(paramter: "尾随闭包的写法") { (parameter) in
     print(parameter)
    }
}
```

`sorted（by :)`的尾随闭包的写法。

```swift
let newArray = names.sorted(){s1,s2 in s1 > s2}
let newArray = names.sorted(){$0>$1}
```



若闭包表达式是函数的唯一参数，在调用该函数时，若采用尾随闭包的写法，则不需要再函数名称之后写一对圆括号`()`

```swift
let newArray = names.sorted{s1,s2 in s1 > s2}
let newArray = names.sorted{$0>$1}

```



#### 值的捕获

闭包可以从定义它的上下文中捕获常量和变量。

```swift
//捕获一个变量的引用
func captureVarReference(){
    
    var name = "jason"
    
    let testCloseure = { //捕获的是变量的引用  捕获的时机是执行闭包的时候,和objc有区别
        //name = "sun"
        print("name is \(name)")
    }
   // testCloseure()
    name = "json"
    testCloseure()
    
    print(name)
}
```



值的捕获，使用**捕获列表**来实现。

```swift
//使用捕获列表获取一个值
func captureVarValue() {
    var name = "jason"
    
    let testCloseure = {
        [_name = name] in//捕获列表
        print("name is \(_name)")
    }
    testCloseure()
    
    name = "json"
    testCloseure()
    
    print(name)
}
```

另外，比较以下两种方式的区别

```swift
//struct 值引用
struct SwiftDemo {
    var name: String
    init(name: String) {
        self.name = name
    }
    func printName() -> () -> () {
        return {
            print(self.name)
        }
    }
}

func structCloseureCaptureTest() {
    
    var demo = SwiftDemo.init(name: "struct")
    let closure = demo.printName()
    demo.name = "structNew"
    closure()
}
```

```swift
//class 引用类型

class SwiftDemoCls {
    var name: String
    init(name: String) {
        self.name = name
    }
    func printName() -> () -> () {
        return {
            print(self.name)
        }
    }
}

func classCloseureCaptureTest() {
   
   let demo: SwiftDemoCls = SwiftDemoCls(name: "class")
    
   let closure = demo.printName()
   demo.name = "classNew"
   closure()
}

```



有时候，闭包并不一定会捕获上下文的变量，示例如下

```swift
func classCloseureCaptureTest1() {
   
   var demo: SwiftDemoCls? = SwiftDemoCls(name: "class")

   typealias nameCloseure = () -> ()
   
   func printName() -> nameCloseure{
      //闭包没有捕获demo
       return {
           print(demo?.name)
       }
   }
    
   let closure = printName()
   closure()
   demo?.name = "class1"
   closure()
   demo = nil
   closure()
}
```



```swift
func classCloseureCaptureTest2() {
   
   var demo: SwiftDemoCls? = SwiftDemoCls(name: "class")

   typealias nameCloseure = () -> ()
   
   func printName() -> nameCloseure{
       //闭包捕获了demo，使得demo的引用计数增加了1
       let demo1 = demo
       return {
           print(demo1?.name)
       }
   }
    
   let closure = printName()
   closure()
   demo?.name = "class1"
   closure()
   demo = nil
   closure()
}
```



#### 闭包是引用类型

函数和闭包是引用类型。无论何时将函数或闭包赋值给常量或变量，实际上都是将该常量或变量设置为对函数或闭包的引用。意味着如果为两个不同的常量或变量分配同一个闭包，那么这两个常量或变量都将引用的是相同的闭包。

```swift
let alsoIncreaseByTen = increaseByTen //引用传递
print(alsoIncreaseByTen())//log:40
let alsoIncreaseByTen1 = increaseByTen
print(alsoIncreaseByTen1())// log:50
```

上述示例中可以看出，函数或闭包是引用类型，常量之间的赋值，其实是引用的传递，不管是`alsoIncreaseByTen`还是`alsoIncreaseByTen1`都引用的是同一个函数指针。因此调用结果会持续递增。



#### 逃逸闭包和非逃逸闭包

一个接受闭包作为参数的函数，该闭包可能在函数返回后才被调用，也就是说这个闭包逃离了函数的作用域，这种闭包称为**逃逸闭包**。当你声明一个接受闭包作为形式参数的函数时，你可以在形式参数前写`@escaping`来明确闭包是允许逃逸。

```swift
//逃逸闭包
func escapeFunc() {
    getData { (data) in
        print("闭包结果返回--\(data)--\(Thread.current)")
    }
}
    
func getData(closure:@escaping (Any) -> Void) {
    print("函数开始执行--\(Thread.current)")
      DispatchQueue.global().async {
         DispatchQueue.main.asyncAfter(deadline: DispatchTime.now()+2, execute: {
             print("执行了闭包---\(Thread.current)")
             closure("网络数据")
         })
      }
      print("函数执行结束---\(Thread.current)")
    }
}
```

以上代码的执行结果是

```swift
函数开始执行--<NSThread: 0x600003da68c0>{number = 1, name = main}
函数执行结束---<NSThread: 0x600003da68c0>{number = 1, name = main}
执行了闭包---<NSThread: 0x600003da68c0>{number = 1, name = main}
闭包结果返回--网络数据--<NSThread: 0x600003da68c0>{number = 1, name = main}
```

可以看出，**逃逸闭包**的生命周期长于调用它的函数的生命周期，即**逃逸闭包**并不会在函数结束时进行释放。



**非逃逸闭包**示例如下：

```swift
//非逃逸闭包
func nonEscapeFunc() {
    handleData { (data) in   //尾随闭包
        print("闭包结果返回--\(data)--\(Thread.current)")
    }
}

func handleData(closure:(Any) -> Void) {
    print("函数开始执行--\(Thread.current)")
    print("执行了闭包---\(Thread.current)")
    closure("处理数据")
    print("函数执行结束---\(Thread.current)")
}
```

 以上代码的执行结果是

```swift
函数开始执行--<NSThread: 0x600001776880>{number = 1, name = main}
执行了闭包---<NSThread: 0x600001776880>{number = 1, name = main}
闭包结果返回--处理数据--<NSThread: 0x600001776880>{number = 1, name = main}
函数执行结束---<NSThread: 0x600001776880>{number = 1, name = main}
```

可以看出，**非逃逸闭包**的生命周期小于调用它的函数的生命周期，即函数结束之前闭包会释放它所捕获的东西。



#### 自动闭包：Autoclosures

`autoclosure`是一个被自动创建的闭包，用于包装作为参数传递给函数的表达式。该表达式被自动创建为：不含参数，返回值省略（根据表达式的返回值决定）`in`关键字省略，方法体中只含表达式的闭包。   当该函数被调用时，自动闭包会返回表达式的值。我们可以通过在函数类型前使用关键字`@autoclosure`把自动闭包外围的花括号`{}`都给去掉。但是**重点是使用`@autoclosure`关键字只限于修饰参数中的闭包，并且该闭包的类型可以有返回值，但绝对不能有参数。**

若对`@autoclosure`关键字搞点事情修饰下不是参数的闭包，会发现报错：`'@autoclosure' may only be used on parameters`。修饰下带有参数的闭包，会发现报错：`Argument type of @autoclosure parameter must be '()'`。

```swift
var nameArray = ["赵云","关羽","张飞","刘备"]
//闭包参数为空 被省略 返回值是String类型由`nameArray.remove(at: 0)`的返回值决定 故省略，同时省略了`in`关键字
let removeClosure = {
    nameArray.remove(at: 0)
}
print("调用`removeClosure`移除之前，数组`nameArray`的个数：\(nameArray.count)。调用`removeClosure`之后移除了字符串：\(removeClosure()) ，数组`nameArray`的个数变为：\(nameArray.count)。综上述可以看出自动闭包允许延迟调用，因为在调用闭包之前，内部代码不会运行")
```

上述示例中，调用`removeClosure`移除数组首元素之前，数组`nameArray`的个数：4。调用`removeClosure`之后移除了字符串：赵云 ，数组`nameArray`的个数变为：3。综上述可以看出自动闭包允许延迟调用，因为在调用闭包之前，内部代码不会运行。

```swift
//延迟调用的另一种形式，定义个拟合自动闭包的函数类型作为参数的函数。先定义闭包，随后在函数体内调用获取值。
static func unknownOperate(operation:()->String) -> Void {
    print("闭包作为参数,输出的删除的数组的字符串:\(operation())")//闭包作为参数,输出的删除的数组的字符串:赵云
}
//将闭包作为参数传递给函数时，在函数中进行数据元素移除的操作
escapeClosure.unknownOperate { () -> String in
    nameArray.remove(at: 0)
}
//更简化的形式
escapeClosure.unknownOperate {nameArray.remove(at: 0)}
```

使用关键字`@autoclosure`标记闭包参数为自动闭包类型的。调用函数时，传递闭包的实参时，可以像传`String`类型的参数一样而不是闭包那样传递。

```
//定义一个自动闭包的形式
static func autoClosure(operation: @autoclosure ()->String){
    print("自动闭包作为参数,输出的删除的数组的字符串:\(operation())")
}

 escapeClosure.autoClosure(operation: nameArray.remove(at: 0))
```

注意：过度使用`autoclosures`会使我们的代码难以理解。 自动闭包同时也允许逃逸。需要同时使用`@autoclosure`和`@escaping`属性。





#### 闭包导致的循环引用

因为闭包本身是**引用类型**，可以被类或其他闭包引用。 而闭包本身可以引用**类或者其他闭包**，这就可以会构成**循环引用**。

 解决方案类似于objc中的，用**weak，unowned**来修饰，示例如下

```swift
class Person {
    var name: String
    lazy var introducePersonCloseure: () ->() = {
        print("the name is \(self.name)")
    }
    
    init(_ name: String) {
        self.name = name
    }
    
    deinit {
        print("Person is deinit")
    }
}

func recyleCircleFunc() {
    var p: Person? = Person.init("Tempo")
    p?.introducePersonCloseure()
    
    p = nil
}
```

以上的例子在p=ni之后，析构函数并不会执行



```swift
class Person {
    var name: String
    lazy var introducePersonCloseure: () ->() = {
        [weak self] in
        print("the name is \(self.name)")
    }
    
    init(_ name: String) {
        self.name = name
    }
    
    deinit {
        print("Person is deinit")
    }
}

func recyleCircleFunc1() {
    var p: Person? = Person.init("Tempo")
    p?.introducePersonCloseure()
    
    p = nil
}
```

加上**weak**修饰之后，析构函数会正常的执行