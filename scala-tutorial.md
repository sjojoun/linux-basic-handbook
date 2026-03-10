# Scala 基础教程

> 作者：JOJO  
> 最后更新：2026 年 3 月 10 日  
> 版本：1.0

---

## 目录

1. [Scala 简介](#1-scala-简介)
2. [环境搭建](#2-环境搭建)
3. [基础语法](#3-基础语法)
4. [控制结构](#4-控制结构)
5. [函数](#5-函数)
6. [面向对象编程](#6-面向对象编程)
7. [集合](#7-集合)
8. [模式匹配](#8-模式匹配)
9. [异常处理](#9-异常处理)
10. [Trait 特质](#10-trait-特质)
11. [函数式编程](#11-函数式编程)
12. [隐式转换](#12-隐式转换)
13. [并发编程](#13-并发编程)
14. [实战示例](#14-实战示例)

---

## 1. Scala 简介

### 什么是 Scala？

Scala（Scalable Language）是一门多范式的编程语言，运行在 JVM 上，结合了**面向对象编程**和**函数式编程**的特性。

### Scala 的特点

- ✅ 运行在 JVM 上，可以调用所有 Java 库
- ✅ 支持面向对象和函数式编程
- ✅ 静态类型，但类型推断让代码简洁
- ✅ 代码简洁，比 Java 少 2-3 倍代码量
- ✅ 支持并发编程（Actor 模型）
- ✅ 大数据领域的标准语言（Spark 用 Scala 编写）

### Scala vs Java

| 特性 | Scala | Java |
|------|-------|------|
| 代码量 | 简洁 | 冗长 |
| 类型系统 | 强大，支持推断 | 较复杂 |
| 函数式 | 原生支持 | Java 8+ 部分支持 |
| 空安全 | Option 类型 | null（容易 NPE） |
| 并发 | Actor 模型 | 线程池 |

---

## 2. 环境搭建

### 安装 JDK

Scala 运行在 JVM 上，需要先安装 JDK 8 或更高版本：

```bash
# 检查 Java 版本
java -version
```

### 安装 Scala

#### 方式 1：使用 SDKMAN（推荐）

```bash
# 安装 SDKMAN
curl -s "https://get.sdkman.io" | bash
source "$HOME/.sdkman/bin/sdkman-init.sh"

# 安装 Scala
sdk install scala

# 查看已安装版本
sdk list scala
```

#### 方式 2：使用包管理器

```bash
# macOS (Homebrew)
brew install scala

# Ubuntu/Debian
sudo apt-get install scala

# Windows (Chocolatey)
choco install scala
```

### 验证安装

```bash
scala -version
```

### 第一个 Scala 程序

```scala
// HelloWorld.scala
object HelloWorld {
  def main(args: Array[String]): Unit = {
    println("Hello, Scala!")
  }
}
```

编译和运行：

```bash
# 编译
scalac HelloWorld.scala

# 运行
scala HelloWorld
```

或使用 Scala 脚本直接运行：

```bash
scala HelloWorld.scala
```

---

## 3. 基础语法

### 变量声明

```scala
// 可变变量（var）
var name: String = "Scala"
name = "Java"  // ✅ 可以修改

// 不可变变量（val）
val version: String = "3.3.0"
// version = "2.13"  // ❌ 编译错误，val 不能修改

// 类型推断（推荐）
var age = 18      // 推断为 Int
val pi = 3.14     // 推断为 Double
```

### 基本数据类型

```scala
// 数值类型
val byteVal: Byte = 127
val shortVal: Short = 32767
val intVal: Int = 2147483647
val longVal: Long = 9223372036854775807L
val floatVal: Float = 3.14f
val doubleVal: Double = 3.1415926

// 字符和布尔
val charVal: Char = 'A'
val boolVal: Boolean = true

// 字符串
val strVal: String = "Hello Scala"
val multiline = """这是
多行
字符串"""
```

### 字符串插值

```scala
val name = "Scala"
val version = "3.3.0"

// s 插值器
println(s"Welcome to $name $version!")
println(s"1 + 1 = ${1 + 1}")

// f 插值器（格式化）
val price = 19.99
println(f"Price: $$${price}%.2f")  // Price: $19.99

// raw 插值器（不转义）
println(raw"Line1\nLine2")  // 输出：Line1\nLine2
```

### 注释

```scala
// 单行注释

/*
 * 多行注释
 */

/**
 * 文档注释（Scaladoc）
 * @param name 参数说明
 * @return 返回值说明
 */
```

---

## 4. 控制结构

### if-else 表达式

```scala
// if-else 是表达式，有返回值
val age = 18
val status = if (age >= 18) "adult" else "minor"

// 多分支
val score = 85
val grade = score match {
  case s if s >= 90 => "A"
  case s if s >= 80 => "B"
  case s if s >= 70 => "C"
  case _ => "D"
}
```

### for 循环

```scala
// 基本循环
for (i <- 1 to 5) {
  println(i)  // 1, 2, 3, 4, 5
}

// 不包含上限
for (i <- 1 until 5) {
  println(i)  // 1, 2, 3, 4
}

// 带守卫（过滤）
for (i <- 1 to 10 if i % 2 == 0) {
  println(i)  // 2, 4, 6, 8, 10
}

// 多重循环
for (i <- 1 to 3; j <- 1 to 3) {
  println(s"($i, $j)")
}

// for 推导式（生成新集合）
val squares = for (i <- 1 to 5) yield i * i
// squares: Vector(1, 4, 9, 16, 25)
```

### while 循环

```scala
var count = 0
while (count < 5) {
  println(count)
  count += 1
}

// do-while
var num = 0
do {
  println(num)
  num += 1
} while (num < 5)
```

---

## 5. 函数

### 函数定义

```scala
// 基本函数
def greet(name: String): String = {
  s"Hello, $name!"
}

// 单行函数可以简写
def add(a: Int, b: Int): Int = a + b

// 无返回值（Unit）
def printHello(): Unit = {
  println("Hello")
}

// 无参数函数可以省略括号
def hello = "Hello"
```

### 默认参数和命名参数

```scala
// 默认参数
def greet(name: String, greeting: String = "Hello"): String = {
  s"$greeting, $name!"
}

greet("Scala")              // Hello, Scala!
greet("Scala", "Hi")        // Hi, Scala!

// 命名参数
def createPerson(name: String, age: Int, city: String) = ???

createPerson(name = "Alice", age = 25, city = "Beijing")
createPerson(age = 25, name = "Alice", city = "Beijing")  // 顺序无关
```

### 可变参数

```scala
def sum(nums: Int*): Int = {
  nums.sum
}

sum(1, 2, 3)        // 6
sum(1, 2, 3, 4, 5)  // 15

// 展开序列
val numbers = Seq(1, 2, 3, 4, 5)
sum(numbers: _*)    // 15
```

### 匿名函数和 Lambda

```scala
// 匿名函数
val add = (a: Int, b: Int) => a + b
add(3, 4)  // 7

// 单参数可以省略括号
val square = (x: Int) => x * x
val square2 = (x: Int) => x * x

// 占位符语法
val numbers = List(1, 2, 3, 4, 5)
numbers.map(_ * 2)  // List(2, 4, 6, 8, 10)
```

### 高阶函数

```scala
// 函数作为参数
def operate(a: Int, b: Int, f: (Int, Int) => Int): Int = {
  f(a, b)
}

operate(3, 4, (x, y) => x + y)  // 7
operate(3, 4, _ * _)            // 12

// 函数作为返回值
def multiplier(factor: Int) = (x: Int) => x * factor
val double = multiplier(2)
double(5)  // 10
```

---

## 6. 面向对象编程

### 类定义

```scala
// 基本类
class Person {
  var name: String = ""
  var age: Int = 0
  
  def greet(): String = s"Hello, I'm $name"
}

// 使用
val p = new Person()
p.name = "Alice"
p.age = 25
println(p.greet())
```

### 构造器

```scala
// 主构造器
class Person(val name: String, var age: Int) {
  // 构造器中的代码会在创建对象时执行
  println(s"Created person: $name")
  
  // 辅助构造器
  def this(name: String) = {
    this(name, 0)  // 必须调用主构造器
  }
}

val p1 = new Person("Alice", 25)
val p2 = new Person("Bob")  // age 默认为 0
```

### 单例对象（object）

```scala
// 伴生对象
class Counter {
  private var count = 0
  def increment(): Unit = count += 1
  def current(): Int = count
}

object Counter {
  // 静态方法和属性
  def apply(): Counter = new Counter()
  
  val VERSION = "1.0"
}

// 使用
val c = Counter()  // 调用 apply 方法
println(Counter.VERSION)
```

### 样例类（Case Class）

```scala
// 样例类（自动实现 equals、hashCode、toString 等）
case class Person(name: String, age: Int)

val p1 = Person("Alice", 25)
val p2 = Person("Alice", 25)

println(p1 == p2)  // true（值相等）
println(p1.name)   // Alice（自动提供 getter）

// copy 方法
val p3 = p1.copy(age = 26)

// 解构
val Person(name, age) = p1
```

### 继承

```scala
// 抽象类
abstract class Animal {
  val name: String
  def speak(): String
}

// 继承
class Dog(override val name: String) extends Animal {
  def speak(): String = s"$name says Woof!"
}

class Cat(override val name: String) extends Animal {
  def speak(): String = s"$name says Meow!"
}

// 使用
val animals: List[Animal] = List(
  new Dog("Buddy"),
  new Cat("Kitty")
)

animals.foreach(a => println(a.speak()))
```

### 访问修饰符

```scala
class MyClass {
  private val privateVal = "private"      // 仅本类可访问
  protected val protectedVal = "protected" // 本类和子类可访问
  public val publicVal = "public"         // 所有人可访问（默认）
  
  private[MyClass] val packagePrivate = "pkg"  // 包内可访问
}
```

---

## 7. 集合

### List（不可变列表）

```scala
// 创建
val list = List(1, 2, 3, 4, 5)
val empty = List()

// 基本操作
list.head       // 1（第一个元素）
list.tail       // List(2, 3, 4, 5)
list.last       // 5
list.init       // List(1, 2, 3, 4)
list.length     // 5
list.reverse    // List(5, 4, 3, 2, 1)

// 添加元素
val newList = 0 :: list           // List(0, 1, 2, 3, 4, 5)
val newList2 = list :+ 6          // List(1, 2, 3, 4, 5, 6)
val combined = list ++ List(6, 7) // List(1, 2, 3, 4, 5, 6, 7)
```

### Set（不可变集合）

```scala
// 创建
val set = Set(1, 2, 3, 3, 4)  // Set(1, 2, 3, 4) 自动去重

// 基本操作
set.contains(3)    // true
set + 5            // Set(1, 2, 3, 4, 5)
set - 2            // Set(1, 3, 4)
set ++ Set(5, 6)   // 并集
set & Set(2, 3)    // 交集
set &~ Set(2, 3)   // 差集
```

### Map（不可变映射）

```scala
// 创建
val map = Map("Alice" -> 25, "Bob" -> 30)
val emptyMap = Map()

// 基本操作
map("Alice")           // 25
map.contains("Alice")  // true
map.get("Charlie")     // None
map + ("Charlie" -> 35)  // 添加
map - ("Bob")            // 删除
map.updated("Alice", 26) // 更新
```

### 可变集合

```scala
import scala.collection.mutable

// 可变 List
val mList = mutable.ListBuffer(1, 2, 3)
mList += 4
mList -= 2
mList(0) = 10

// 可变 Set
val mSet = mutable.Set(1, 2, 3)
mSet += 4
mSet -= 2

// 可变 Map
val mMap = mutable.Map("Alice" -> 25)
mMap("Bob") = 30
mMap.update("Alice", 26)
```

### 常用集合操作

```scala
val numbers = List(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)

// map（转换）
numbers.map(_ * 2)        // List(2, 4, 6, 8, 10, 12, 14, 16, 18, 20)

// filter（过滤）
numbers.filter(_ % 2 == 0)  // List(2, 4, 6, 8, 10)

// flatMap（扁平化）
List(1, 2, 3).flatMap(x => List(x, x * 10))
// List(1, 10, 2, 20, 3, 30)

// reduce（归约）
numbers.reduce(_ + _)     // 55
numbers.reduceLeft(_ max _)  // 10

// fold（带初始值的归约）
numbers.fold(0)(_ + _)    // 55

// groupBy（分组）
numbers.groupBy(_ % 2)    // Map(1 -> List(1, 3, 5, 7, 9), 0 -> List(2, 4, 6, 8, 10))

// partition（分区）
numbers.partition(_ % 2 == 0)  // (List(2, 4, 6, 8, 10), List(1, 3, 5, 7, 9))

// take/drop（取前 N 个/丢弃前 N 个）
numbers.take(3)   // List(1, 2, 3)
numbers.drop(3)   // List(4, 5, 6, 7, 8, 9, 10)

// sorted/sortBy（排序）
numbers.sorted               // 升序
numbers.sortBy(-_)           // 降序
numbers.sortWith(_ > _)      // 降序
```

---

## 8. 模式匹配

### 基本模式匹配

```scala
val num = 3

num match {
  case 1 => println("One")
  case 2 => println("Two")
  case 3 => println("Three")
  case _ => println("Other")
}
```

### 类型匹配

```scala
def getType(x: Any): String = x match {
  case s: String => s"String: $s"
  case i: Int => s"Int: $i"
  case d: Double => s"Double: $d"
  case _ => "Unknown"
}
```

### 样例类匹配

```scala
case class Person(name: String, age: Int)
case class Dog(name: String)

val entities: List[Any] = List(
  Person("Alice", 25),
  Dog("Buddy"),
  "Hello"
)

entities.foreach {
  case Person(name, age) => println(s"Person: $name, $age")
  case Dog(name) => println(s"Dog: $name")
  case _ => println("Unknown")
}
```

### 守卫模式

```scala
def describe(x: Int): String = x match {
  case n if n < 0 => "negative"
  case 0 => "zero"
  case n if n > 0 && n < 10 => "single digit"
  case _ => "other"
}
```

### Option 模式匹配

```scala
val maybeNum: Option[Int] = Some(42)

maybeNum match {
  case Some(n) => println(s"Got: $n")
  case None => println("Got nothing")
}
```

---

## 9. 异常处理

### try-catch-finally

```scala
import scala.io.Source

try {
  val file = Source.fromFile("test.txt")
  val content = file.mkString
  file.close()
} catch {
  case e: java.io.FileNotFoundException =>
    println(s"File not found: ${e.getMessage}")
  case e: Exception =>
    println(s"Other error: ${e.getMessage}")
} finally {
  println("Cleanup done")
}
```

### Try 类型（推荐）

```scala
import scala.util.{Try, Success, Failure}

def divide(a: Int, b: Int): Try[Int] = Try(a / b)

divide(10, 2) match {
  case Success(result) => println(s"Result: $result")
  case Failure(e) => println(s"Error: ${e.getMessage}")
}

// 链式调用
val result = for {
  a <- Try("10".toInt)
  b <- Try("2".toInt)
  r <- divide(a, b)
} yield r

result.getOrElse(0)  // 失败时返回默认值
```

### 抛出异常

```scala
def validateAge(age: Int): Unit = {
  if (age < 0) {
    throw new IllegalArgumentException("Age cannot be negative")
  }
}
```

---

## 10. Trait 特质

### 基本 Trait

```scala
// 定义 Trait
trait Logger {
  def log(msg: String): Unit
  def info(msg: String): Unit = log(s"[INFO] $msg")
  def error(msg: String): Unit = log(s"[ERROR] $msg")
}

// 混入 Trait
class ConsoleLogger extends Logger {
  def log(msg: String): Unit = println(msg)
}

val logger = new ConsoleLogger()
logger.info("Application started")
```

### 多个 Trait

```scala
trait TimestampLogger extends Logger {
  abstract override def log(msg: String): Unit = {
    super.log(s"${java.time.LocalDateTime.now()} $msg")
  }
}

trait ShortLogger extends Logger {
  abstract override def log(msg: String): Unit = {
    super.log(msg.take(10))
  }
}

// 混入多个 Trait（从右到左执行）
class FileLogger extends Logger {
  def log(msg: String): Unit = println(msg)
}

val logger = new FileLogger with TimestampLogger with ShortLogger
```

### Trait 作为接口

```scala
trait Shape {
  def area: Double
  def perimeter: Double
}

case class Rectangle(width: Double, height: Double) extends Shape {
  def area: Double = width * height
  def perimeter: Double = 2 * (width + height)
}

case class Circle(radius: Double) extends Shape {
  def area: Double = Math.PI * radius * radius
  def perimeter: Double = 2 * Math.PI * radius
}
```

---

## 11. 函数式编程

### 纯函数

```scala
// 纯函数：相同的输入总是得到相同的输出，没有副作用
def add(a: Int, b: Int): Int = a + b

// 不纯：有副作用
var counter = 0
def increment(): Int = {
  counter += 1  // 修改外部状态
  counter
}
```

### 不可变性

```scala
// 使用 val 和不可变集合
val numbers = Vector(1, 2, 3)
val newNumbers = numbers :+ 4  // 创建新集合，原集合不变
```

### 递归

```scala
// 尾递归（推荐）
import scala.annotation.tailrec

@tailrec
def factorial(n: Int, acc: Int = 1): Int = {
  if (n <= 1) acc
  else factorial(n - 1, n * acc)
}

factorial(5)  // 120

// 普通递归
def fib(n: Int): Int = {
  if (n <= 1) n
  else fib(n - 1) + fib(n - 2)
}
```

### 柯里化

```scala
// 柯里化函数
def add(a: Int)(b: Int): Int = a + b

val add5 = add(5)_
add5(3)  // 8

// 应用
def filter(xs: List[Int])(p: Int => Boolean): List[Int] = {
  xs.filter(p)
}

val filterEven = filter(List(1, 2, 3, 4, 5))(_ % 2 == 0)
```

---

## 12. 隐式转换

### 隐式参数

```scala
def greet(name: String)(implicit greeting: String): String = {
  s"$greeting, $name!"
}

implicit val defaultGreeting: String = "Hello"
greet("Scala")  // Hello, Scala!
greet("Scala")("Hi")  // Hi, Scala!
```

### 隐式转换函数

```scala
// 定义隐式转换
implicit def intToString(x: Int): String = x.toString

// 自动转换
val s: String = 42  // 自动转换为 "42"
```

### 隐式类（扩展方法）

```scala
implicit class StringEnhancer(s: String) {
  def reverseWords: String = s.split(" ").reverse.mkString(" ")
  def words: List[String] = s.split(" ").toList
}

"Hello World".reverseWords  // "World Hello"
"Scala is great".words      // List("Scala", "is", "great")
```

### Context Bounds

```scala
// 类型类模式
def printList[T](list: List[T])(implicit show: Show[T]): Unit = {
  list.foreach(x => println(show.show(x)))
}

// 简写（Context Bounds）
def printList2[T: Show](list: List[T]): Unit = {
  list.foreach(x => println(implicitly[Show[T]].show(x)))
}
```

---

## 13. 并发编程

### Future 基础

```scala
import scala.concurrent.{Future, Await}
import scala.concurrent.ExecutionContext.Implicits.global
import scala.concurrent.duration._

// 创建 Future
val future: Future[Int] = Future {
  Thread.sleep(1000)
  42
}

// 回调
future.onSuccess {
  case result => println(s"Result: $result")
}

future.onFailure {
  case e => println(s"Error: ${e.getMessage}")
}

// 等待结果
val result = Await.result(future, 2.seconds)

// 组合 Future
val f1 = Future(10)
val f2 = Future(20)

val combined = for {
  a <- f1
  b <- f2
} yield a + b
```

### Actor 模型（Akka）

```scala
import akka.actor.{Actor, ActorSystem, Props}

// 定义 Actor
class MyActor extends Actor {
  def receive: Receive = {
    case "hello" => println("Hello!")
    case x: Int => println(s"Got number: $x")
    case _ => println("Unknown message")
  }
}

// 创建 Actor 系统
val system = ActorSystem("MySystem")
val actor = system.actorOf(Props[MyActor], "myActor")

// 发送消息
actor ! "hello"
actor ! 42
```

### Parallel Collections

```scala
// 并行集合
val numbers = (1 to 1000000).toList

// 串行
val sum1 = numbers.filter(_ % 2 == 0).map(_ * 2).sum

// 并行
val sum2 = numbers.par.filter(_ % 2 == 0).map(_ * 2).sum
```

---

## 14. 实战示例

### 示例 1：Word Count

```scala
object WordCount {
  def main(args: Array[String]): Unit = {
    val text = """
      Scala is a general-purpose programming language.
      Scala supports functional programming.
      Scala runs on JVM.
    """
    
    val wordCount = text
      .toLowerCase
      .split("\\W+")
      .filter(_.nonEmpty)
      .groupBy(identity)
      .mapValues(_.length)
      .toSeq
      .sortBy(-_._2)
    
    wordCount.foreach {
      case (word, count) => println(s"$word: $count")
    }
  }
}
```

### 示例 2：简易计算器

```scala
sealed trait Operation
case object Add extends Operation
case object Subtract extends Operation
case object Multiply extends Operation
case object Divide extends Operation

object Calculator {
  def calculate(a: Double, b: Double, op: Operation): Option[Double] = op match {
    case Add => Some(a + b)
    case Subtract => Some(a - b)
    case Multiply => Some(a * b)
    case Divide => if (b != 0) Some(a / b) else None
  }
  
  def main(args: Array[String]): Unit = {
    println(calculate(10, 5, Add))       // Some(15.0)
    println(calculate(10, 0, Divide))    // None
  }
}
```

### 示例 3：数据处理管道

```scala
case class User(id: Int, name: String, age: Int, active: Boolean)

object DataPipeline {
  def main(args: Array[String]): Unit = {
    val users = List(
      User(1, "Alice", 25, true),
      User(2, "Bob", 30, false),
      User(3, "Charlie", 35, true),
      User(4, "David", 28, true)
    )
    
    // 数据处理管道
    val result = users
      .filter(_.active)                    // 只保留活跃用户
      .filter(_.age >= 30)                 // 年龄 >= 30
      .map(_.name.toUpperCase)             // 转大写
      .sorted                              // 排序
      .take(2)                             // 取前 2 个
    
    result.foreach(println)  // CHARLIE
  }
}
```

### 示例 4：JSON 解析（使用 circe）

```scala
import io.circe._
import io.circe.generic.auto._
import io.circe.parser._

case class Person(name: String, age: Int)

object JsonExample {
  def main(args: Array[String]): Unit = {
    // 序列化
    val person = Person("Alice", 25)
    val json = person.asJson.noSpaces
    println(json)  // {"name":"Alice","age":25}
    
    // 反序列化
    val jsonString = """{"name":"Bob","age":30}"""
    val decoded = decode[Person](jsonString)
    decoded match {
      case Right(p) => println(p)
      case Left(e) => println(s"Error: $e")
    }
  }
}
```

---

## 附录：常用命令

### 编译和运行

```bash
# 编译
scalac HelloWorld.scala

# 运行
scala HelloWorld

# 直接运行脚本
scala HelloWorld.scala

# 进入 REPL
scala

# 使用 sbt（构建工具）
sbt compile
sbt run
sbt console
```

### 常用库

```scala
// build.sbt
libraryDependencies ++= Seq(
  "org.scalatest" %% "scalatest" % "3.2.17" % "test",  // 测试框架
  "io.circe" %% "circe-core" % "0.14.6",               // JSON
  "io.circe" %% "circe-generic" % "0.14.6",
  "io.circe" %% "circe-parser" % "0.14.6",
  "com.typesafe.akka" %% "akka-actor" % "2.8.5",       // Actor
  "org.typelevel" %% "cats-core" % "2.10.0"            // 函数式库
)
```

---

## 学习资源

- **官方文档**: https://docs.scala-lang.org/
- **Scala 3 书籍**: https://docs.scala-lang.org/scala3/book/introduction.html
- **Rock the JVM**: https://rockthejvm.com/
- **Scala Exercises**: https://www.scala-exercises.org/
- **Coursera Scala 专项**: https://www.coursera.org/specializations/scala

---

**祝你 Scala 学习愉快！** 🎉

*作者：JOJO*
