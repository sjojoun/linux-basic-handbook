# Flink Scala 零基础入门教程 - 手把手教你写第一个实时任务

> 📚 完全零基础友好！使用 Scala 语言，跟着步骤做就能学会

![Flink](https://img.shields.io/badge/Flink-入门教程-E6526F?style=for-the-badge)
![Scala](https://img.shields.io/badge/语言-Scala-DC322F?style=for-the-badge)
![难度](https://img.shields.io/badge/难度-⭐⭐-yellow?style=for-the-badge)

---

## 📖 先看这个！什么是 Flink？

### 用大白话解释

想象你开了一家**奶茶店**：

**传统方式（批处理）**：
```
一天结束 → 数一数今天卖了多少杯 → 算总收入
```

**Flink 方式（流处理）**：
```
每卖一杯 → 立刻记下来 → 实时显示今天卖了多少杯
```

### Flink 能做什么？

```
┌─────────────┐     ┌──────────────┐     ┌─────────────┐
│  实时数据   │ ──→ │    Flink     │ ──→ │   结果输出   │
│  (源源不断) │     │  (实时计算)   │     │ (实时看到)   │
└─────────────┘     └──────────────┘     └─────────────┘
     ↓                      ↓                     ↓
  订单数据                计算总额              大屏展示
  日志数据                统计人数              存入数据库
  传感器数据              发现异常              发送警报
```

### 为什么用 Scala？

| 对比项 | Java | Scala |
|--------|------|-------|
| 代码量 | 较多 | **简洁 50%+** |
| 学习难度 | 中等 | 稍高（但值得） |
| Flink 支持 | ✅ 完美 | ✅ 更简洁 |
| 函数式编程 | ❌ 麻烦 | ✅ 原生支持 |

**Scala 代码对比：**

```java
// Java 版本
stream.filter(s -> s.contains("hello"))
      .map(s -> new Tuple2<>(s, 1))
      .keyBy(t -> t.f0)
      .sum(1);
```

```scala
// Scala 版本 - 更简洁！
stream.filter(_.contains("hello"))
      .map(s => (s, 1))
      .keyBy(_._1)
      .sum(1)
```

---

## 🛠️ 第一步：准备工作

### 需要安装什么？

**最少只需要 2 个软件：**

1. **JDK 8** - 运行 Java/Scala 程序的环境
2. **Maven** - 自动下载项目需要的库

**可选（有更好）：**
- IntelliJ IDEA - 写代码的工具（要装 Scala 插件）
- Kafka - 如果要从消息队列读取数据

### 检查环境是否准备好

打开命令行（Windows 按 Win+R，输入 cmd，回车）：

```bash
# 检查 Java
java -version

# 应该看到类似：
# java version "1.8.0_xxx"
```

```bash
# 检查 Maven
mvn -version

# 应该看到类似：
# Apache Maven 3.x.x
```

**如果显示"不是内部命令"，需要先安装：**

- JDK 下载：https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html
- Maven 下载：https://maven.apache.org/download.cgi

---

## 📁 第二步：创建 Scala 项目

### 方法 1：用 IDEA（强烈推荐）

1. 打开 IDEA
2. 点击 `New Project`
3. 左边选 `Maven`
4. 勾选 `Create from archetype` → 选择 `scala-archetype-simple`
5. 点击 `Next`
6. 填写：
   - GroupId: `com.example`
   - ArtifactId: `flink-scala-demo`
7. 点击 `Finish`

### 方法 2：手动创建

```bash
# 创建文件夹
mkdir flink-scala-demo
cd flink-scala-demo

# 创建目录结构
mkdir -p src/main/scala/com/example
```

### 创建 pom.xml 文件

在项目根目录创建 `pom.xml`：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project>
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>flink-scala-demo</artifactId>
    <version>1.0-SNAPSHOT</version>
    
    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <flink.version>1.14.0</flink.version>
        <scala.version>2.11.12</scala.version>
        <scala.binary.version>2.11</scala.binary.version>
    </properties>
    
    <dependencies>
        <!-- Scala 库 -->
        <dependency>
            <groupId>org.scala-lang</groupId>
            <artifactId>scala-library</artifactId>
            <version>${scala.version}</version>
        </dependency>
        
        <!-- Flink 核心 -->
        <dependency>
            <groupId>org.apache.flink</groupId>
            <artifactId>flink-scala_${scala.binary.version}</artifactId>
            <version>${flink.version}</version>
        </dependency>
        
        <!-- Flink 流处理 -->
        <dependency>
            <groupId>org.apache.flink</groupId>
            <artifactId>flink-streaming-scala_${scala.binary.version}</artifactId>
            <version>${flink.version}</version>
        </dependency>
        
        <!-- Flink 客户端 -->
        <dependency>
            <groupId>org.apache.flink</groupId>
            <artifactId>flink-clients_${scala.binary.version}</artifactId>
            <version>${flink.version}</version>
        </dependency>
        
        <!-- Kafka 连接器 -->
        <dependency>
            <groupId>org.apache.flink</groupId>
            <artifactId>flink-connector-kafka_${scala.binary.version}</artifactId>
            <version>${flink.version}</version>
        </dependency>
        
        <!-- Redis 连接器 -->
        <dependency>
            <groupId>org.apache.bahir</groupId>
            <artifactId>flink-connector-redis_${scala.binary.version}</artifactId>
            <version>1.0</version>
        </dependency>
        
        <!-- MySQL 连接器 -->
        <dependency>
            <groupId>org.apache.flink</groupId>
            <artifactId>flink-connector-jdbc_${scala.binary.version}</artifactId>
            <version>${flink.version}</version>
        </dependency>
        
        <!-- MySQL 驱动 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.28</version>
        </dependency>
        
        <!-- JSON 解析 -->
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.78</version>
        </dependency>
    </dependencies>
    
    <build>
        <plugins>
            <!-- Scala 编译插件 -->
            <plugin>
                <groupId>net.alchim31.maven</groupId>
                <artifactId>scala-maven-plugin</artifactId>
                <version>4.8.1</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>compile</goal>
                            <goal>testCompile</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            
            <!-- 打包插件 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.4</version>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```

---

## 💻 第三步：写第一个 Flink Scala 程序

### 最简单的例子：单词计数

**任务：** 数一数输入的句子中每个单词出现了几次

**输入：** `hello world hello flink`

**输出：** 
- hello: 2 次
- world: 1 次
- flink: 1 次

### 完整代码

在 `src/main/scala/com/example` 目录下创建 `WordCount.scala`：

```scala
package com.example

import org.apache.flink.api.scala._
import org.apache.flink.streaming.api.scala.StreamExecutionEnvironment

/**
 * Flink Scala 入门示例 - 单词计数
 * 
 * 功能：统计每个单词出现的次数
 */
object WordCount {
  
  def main(args: Array[String]): Unit = {
    // 1. 创建执行环境
    // 想象这是 Flink 的"工作台"
    val env = StreamExecutionEnvironment.getExecutionEnvironment
    
    // 2. 创建数据源
    // 这里用简单的文本数据，实际可以用 Kafka、文件等
    val textStream = env.fromElements(
      "hello world hello flink",
      "flink is awesome",
      "hello flink world"
    )
    
    // 3. 处理数据
    // 把句子拆成单词，然后计数
    val result = textStream
      // 第一步：把句子拆成单词
      .flatMap { sentence =>
        sentence.split(" ")  // 按空格拆分
      }
      // 第二步：变成 (单词，1) 的格式
      .map { word =>
        (word, 1)
      }
      // 第三步：按单词分组
      .keyBy(_._1)  // _1 表示元组的第一个字段（单词）
      // 第四步：累加计数
      .sum(1)       // 1 表示元组的第二个字段（计数）
    
    // 4. 打印结果
    result.print()
    
    // 5. 执行任务
    env.execute("WordCount Demo")
  }
}
```

### 运行代码

**在 IDEA 中：**
1. 右键点击 `WordCount.scala`
2. 选择 `Run 'WordCount'`
3. 看控制台输出

**在命令行：**
```bash
# 编译
mvn clean compile

# 运行
mvn exec:java -Dexec.mainClass="com.example.WordCount"
```

### 输出结果

```
> (hello,1)
> (world,1)
> (hello,2)
> (flink,1)
> (flink,2)
> (is,1)
> (awesome,1)
> (flink,3)
> (world,2)
```

**解读：**
- `hello` 第一次出现 → (hello,1)
- `hello` 第二次出现 → (hello,2) ← 累加了！
- `flink` 第三次出现 → (flink,3) ← 已经出现 3 次了

---

## 🎯 第四步：理解核心概念（Scala 版）

### 概念 1：DataStream（数据流）

**通俗理解：** 一条源源不断的数据河流

```scala
// 创建数据流
val stream = env.fromElements("数据 1", "数据 2", "数据 3")

// 就像：
// 河流 → 数据 1 → 数据 2 → 数据 3 → ...
```

### 概念 2：Transformation（转换）

**通俗理解：** 对数据流进行加工

```scala
// 常见的转换操作：

// 1. map - 一对一转换
// 输入："123" → 输出：123 (数字)
stream.map(s => s.toInt)

// 2. filter - 过滤
// 只保留包含"hello"的数据
stream.filter(_.contains("hello"))

// 3. flatMap - 一对多转换
// 输入："a b c" → 输出："a", "b", "c"
stream.flatMap { sentence =>
  sentence.split(" ")
}

// 4. keyBy - 分组
// 按第一个字段分组
stream.keyBy(_._1)

// 5. sum - 累加
// 对第二个字段累加
stream.sum(1)
```

### Scala 特有的简洁写法

```scala
// 完整写法
stream.map(word => (word, 1))

// 简化写法（省略参数名）
stream.map(word => (word, 1))

// 再简化（使用占位符）
stream.map((_, 1))
```

```scala
// 完整写法
stream.filter(s => s.contains("hello"))

// 简化写法
stream.filter(_.contains("hello"))
```

### 概念 3：Case Class（样例类）

**通俗理解：** 用来定义数据结构的"模板"

```scala
// 定义订单数据结构
case class Order(
  id: Long,              // 订单 ID
  userId: String,        // 用户 ID
  amount: Double,        // 金额
  createTime: Long       // 创建时间
)

// 使用
val order = Order(1L, "user123", 299.00, System.currentTimeMillis())

// 访问字段
println(order.id)        // 输出：1
println(order.amount)    // 输出：299.0
```

**好处：**
- 代码简洁（不用写 getter/setter）
- 自动支持模式匹配
- 可以方便地复制和修改

```scala
// 复制并修改
val newOrder = order.copy(amount = 399.00)
```

### 概念 4：State（状态）

**通俗理解：** Flink 的"记忆力"

```scala
// 没有状态：记不住之前发生了什么
// 输入：1, 2, 3 → 输出：1, 2, 3（只是原样输出）

// 有状态：能记住之前的数据
// 输入：1, 2, 3 → 输出：1, 3, 6（累加结果）
```

**代码示例：**

```scala
import org.apache.flink.api.common.state.{ValueState, ValueStateDescriptor}
import org.apache.flink.api.scala._
import org.apache.flink.configuration.Configuration
import org.apache.flink.streaming.api.functions.RichMapFunction

// 使用 ValueState 记住累加和
stream
  .keyBy(_._1)
  .map(new RichMapFunction[(String, Int), (String, Int)] {
    var sumState: ValueState[Int] = _
    
    override def open(parameters: Configuration): Unit = {
      // 初始化状态（就像准备一个小本本）
      sumState = getRuntimeContext.getState(
        new ValueStateDescriptor[Int]("sum", classOf[Int])
      )
    }
    
    override def map(value: (String, Int)): (String, Int) = {
      // 读取之前的和
      val currentSum = if (sumState.value() == null) 0 else sumState.value()
      
      // 累加
      val newSum = currentSum + value._2
      
      // 更新状态（记在小本本上）
      sumState.update(newSum)
      
      (value._1, newSum)
    }
  })
```

### 概念 5：Window（窗口）

**通俗理解：** 把数据切成一段一段来处理

```
时间线：
|----|----|----|----|
   窗口 1  窗口 2  窗口 3

窗口 1：处理 9:00-9:05 的数据
窗口 2：处理 9:05-9:10 的数据
窗口 3：处理 9:10-9:15 的数据
```

**代码示例：**

```scala
import org.apache.flink.streaming.api.windowing.time.Time

// 每 5 分钟统计一次
stream
  .keyBy(_._1)
  .timeWindow(Time.minutes(5))  // 5 分钟窗口
  .sum(2)  // 累加第三个字段
```

---

## 📦 第五步：实战 - 订单处理系统（Scala 版）

### 业务需求

```
1. 从 Kafka 读取订单数据
2. 计算销售总额
3. 计算退款总额
4. 结果存入 Redis
5. 取消订单存入 MySQL
```

### 数据格式

**Kafka 中的订单数据（JSON 格式）：**

```json
{
  "id": 10001,
  "consignee": "张三",
  "consignee_tel": "13800138000",
  "final_total_amount": 299.00,
  "order_status": "1005",
  "create_time": "2026-03-11 10:30:00",
  "operate_time": "2026-03-11 10:35:00",
  "feight_fee": 10.00
}
```

**订单状态说明：**

| 状态码 | 含义 | 怎么处理 |
|--------|------|----------|
| 1003 | 已取消 | 存入 MySQL |
| 1005 | 已完成 | 计入销售额 |
| 1006 | 已退款 | 计入退款额 |

### 完整代码

在 `src/main/scala/com/example` 目录下创建 `OrderProcessTask.scala`：

```scala
package com.example

import java.time.format.DateTimeFormatter
import java.time.{LocalDateTime, ZoneId}
import java.util.Properties

import com.alibaba.fastjson.JSON
import org.apache.flink.api.common.eventtime.WatermarkStrategy
import org.apache.flink.api.common.functions.RichMapFunction
import org.apache.flink.api.common.serialization.SimpleStringSchema
import org.apache.flink.api.common.state.{ValueState, ValueStateDescriptor}
import org.apache.flink.configuration.Configuration
import org.apache.flink.connector.jdbc.{JdbcConnectionOptions, JdbcSink}
import org.apache.flink.connector.kafka.FlinkKafkaConsumer
import org.apache.flink.streaming.api.scala._
import org.apache.flink.streaming.connectors.redis.RedisSink
import org.apache.flink.streaming.connectors.redis.common.config.FlinkJedisPoolConfig
import org.apache.flink.streaming.connectors.redis.common.mapper.{RedisCommand, RedisCommandDescription, RedisMapper}

import java.sql.PreparedStatement

/**
 * 订单实时处理任务
 * 
 * 功能：
 * 1. 从 Kafka 读取订单数据
 * 2. 计算销售总额和退款总额，存入 Redis
 * 3. 取消订单存入 MySQL
 */
object OrderProcessTask {
  
  // ========== 第一步：定义数据模型 ==========
  
  /**
   * 订单数据
   * 
   * @param id 订单 ID
   * @param consignee 收货人
   * @param consigneeTel 联系电话
   * @param amount 订单金额
   * @param status 订单状态
   * @param createTime 创建时间
   * @param operateTime 操作时间
   * @param freightFee 运费
   * @param eventTime 事件时间（用于水位线）
   */
  case class Order(
    id: Long,
    consignee: String,
    consigneeTel: String,
    amount: Double,
    status: String,
    createTime: String,
    operateTime: String,
    freightFee: Double,
    eventTime: Long
  )
  
  /**
   * 统计结果
   * 
   * @param key 键（如 total_sales）
   * @param value 值（如 12345.67）
   */
  case class StatResult(key: String, value: Double)
  
  // ========== 第二步：主函数 ==========
  
  def main(args: Array[String]): Unit = {
    
    // ========== 第三步：创建执行环境 ==========
    val env = StreamExecutionEnvironment.getExecutionEnvironment
    env.setParallelism(1)  // 设置并行度为 1（简单起见）
    
    // ========== 第四步：配置 Kafka 数据源 ==========
    val props = new Properties()
    props.setProperty("bootstrap.servers", "192.168.12.41:9092")  // Kafka 地址
    props.setProperty("group.id", "flink-order-group")  // 消费者组
    
    // 创建 Kafka 数据流
    val kafkaStream = env.addSource(
      new FlinkKafkaConsumer[String](
        "order",              // Topic 名称
        new SimpleStringSchema(),  // 字符串格式
        props
      )
    )
    
    // ========== 第五步：解析 JSON 数据 ==========
    val orderStream = kafkaStream
      .map { jsonStr =>
        try {
          // 解析 JSON
          val json = JSON.parseObject(jsonStr)
          
          // 提取字段
          val id = json.getLong("id")
          val consignee = json.getString("consignee")
          val consigneeTel = json.getString("consignee_tel")
          val amount = json.getDouble("final_total_amount")
          val status = json.getString("order_status")
          val createTime = json.getString("create_time")
          val operateTime = json.getString("operate_time")
          val freightFee = json.getDouble("feight_fee")
          
          // 转换时间为时间戳
          val formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")
          val createTs = LocalDateTime.parse(createTime, formatter)
            .atZone(ZoneId.systemDefault())
            .toInstant
            .toEpochMilli
          
          val operateTs = LocalDateTime.parse(operateTime, formatter)
            .atZone(ZoneId.systemDefault())
            .toInstant
            .toEpochMilli
          
          // 取较晚的时间作为事件时间
          val eventTime = math.max(createTs, operateTs)
          
          // 创建订单对象
          Order(id, consignee, consigneeTel, amount, status, 
                createTime, operateTime, freightFee, eventTime)
          
        } catch {
          case _: Exception => null  // 解析失败返回 null
        }
      }
      .filter(_ != null)  // 过滤掉解析失败的
    
    // ========== 第六步：业务分流处理 ==========
    
    // 6.1 正常订单 → 计算销售额
    val salesStream = orderStream
      .filter { order =>
        // 过滤掉取消、已完成、退款的订单
        order.status != "1003" && 
        order.status != "1005" && 
        order.status != "1006"
      }
      .map { order =>
        StatResult("total_sales", order.amount)
      }
      .keyBy(_.key)
      .map(new RichMapFunction[StatResult, StatResult] {
        var sumState: ValueState[Double] = _
        
        override def open(parameters: Configuration): Unit = {
          // 初始化状态
          sumState = getRuntimeContext.getState(
            new ValueStateDescriptor[Double]("sales_sum", classOf[Double])
          )
        }
        
        override def map(value: StatResult): StatResult = {
          // 读取当前和
          val currentSum = if (sumState.value() == null) 0.0 else sumState.value()
          
          // 累加
          val newSum = currentSum + value.value
          
          // 更新状态
          sumState.update(newSum)
          
          StatResult(value.key, newSum)
        }
      })
    
    // 6.2 退款订单 → 计算退款额
    val refundStream = orderStream
      .filter(_.status == "1006")  // 只保留退款订单
      .map { order =>
        StatResult("total_refund", order.amount)
      }
      .keyBy(_.key)
      .map(new RichMapFunction[StatResult, StatResult] {
        var sumState: ValueState[Double] = _
        
        override def open(parameters: Configuration): Unit = {
          sumState = getRuntimeContext.getState(
            new ValueStateDescriptor[Double]("refund_sum", classOf[Double])
          )
        }
        
        override def map(value: StatResult): StatResult = {
          val currentSum = if (sumState.value() == null) 0.0 else sumState.value()
          val newSum = currentSum + value.value
          sumState.update(newSum)
          StatResult(value.key, newSum)
        }
      })
    
    // 6.3 取消订单 → 准备写入 MySQL
    val cancelStream = orderStream
      .filter(_.status == "1003")  // 只保留取消订单
    
    // ========== 第七步：配置输出 ==========
    
    // 7.1 Redis 配置
    val redisConfig = new FlinkJedisPoolConfig.Builder()
      .setHost("192.168.12.41")
      .setPort(6379)
      .build()
    
    // 7.2 写入 Redis
    salesStream.addSink(
      new RedisSink[StatResult](
        redisConfig, 
        new RedisMapper[StatResult] {
          override def getCommandDescription: RedisCommandDescription = {
            new RedisCommandDescription(RedisCommand.SET)
          }
          
          override def getKeyFromData(data: StatResult): String = {
            data.key
          }
          
          override def getValueFromData(data: StatResult): String = {
            f"${data.value}%.2f"  // 保留 2 位小数
          }
        }
      )
    )
    
    refundStream.addSink(
      new RedisSink[StatResult](
        redisConfig,
        new RedisMapper[StatResult] {
          override def getCommandDescription: RedisCommandDescription = {
            new RedisCommandDescription(RedisCommand.SET)
          }
          
          override def getKeyFromData(data: StatResult): String = data.key
          
          override def getValueFromData(data: StatResult): String = {
            f"${data.value}%.2f"
          }
        }
      )
    )
    
    // 7.3 写入 MySQL
    cancelStream.addSink(
      JdbcSink.sink(
        // SQL 语句
        """
          INSERT INTO order_cancel (id, consignee, consignee_tel, amount, freight_fee) 
          VALUES (?, ?, ?, ?, ?) 
          ON DUPLICATE KEY UPDATE consignee=VALUES(consignee)
        """,
        
        // 参数绑定
        new org.apache.flink.connector.jdbc.JdbcStatementBuilder[Order] {
          override def accept(ps: PreparedStatement, order: Order): Unit = {
            ps.setLong(1, order.id)
            ps.setString(2, order.consignee)
            ps.setString(3, order.consigneeTel)
            ps.setDouble(4, order.amount)
            ps.setDouble(5, order.freightFee)
          }
        },
        
        // 连接配置
        new JdbcConnectionOptions.JdbcConnectionOptionsBuilder()
          .withUrl("jdbc:mysql://192.168.12.41:3306/shtd_result?useSSL=false&characterEncoding=utf8")
          .withDriverName("com.mysql.cj.jdbc.Driver")
          .withUsername("root")
          .withPassword("123456")
          .build()
      )
    )
    
    // ========== 第八步：启动任务 ==========
    println("任务已启动...")
    env.execute("订单处理任务")
  }
}
```

---

## 🚀 第六步：运行任务

### 本地运行（开发测试）

```bash
# 1. 编译项目
mvn clean package

# 2. 运行任务
java -jar target/flink-scala-demo-1.0-SNAPSHOT.jar
```

### 提交到 Flink 集群（生产环境）

```bash
# 1. 启动 Flink 集群
cd /opt/flink
./bin/start-cluster.sh

# 2. 提交任务
./bin/flink run \
  -c com.example.OrderProcessTask \
  -p 4 \
  target/flink-scala-demo-1.0-SNAPSHOT.jar

# 3. 查看任务列表
./bin/flink list

# 4. 查看任务状态
./bin/flink list -r

# 5. 停止任务
./bin/flink cancel <job-id>
```

### 查看结果

**查看 Redis 结果：**

```bash
# 连接 Redis
redis-cli -h 192.168.12.41

# 查看销售额
GET total_sales
# 输出：12345.67

# 查看退款额
GET total_refund
# 输出：890.00
```

**查看 MySQL 结果：**

```sql
-- 连接 MySQL
mysql -h 192.168.12.41 -u root -p

-- 查询取消订单
SELECT * FROM order_cancel;
```

---

## ❓ 常见问题解答

### Q1: 报错"找不到 Scala 库"

**原因：** pom.xml 中没有添加 Scala 依赖

**解决：** 确保有：

```xml
<dependency>
    <groupId>org.scala-lang</groupId>
    <artifactId>scala-library</artifactId>
    <version>2.11.12</version>
</dependency>
```

### Q2: 报错"找不到主类"

**原因：** 打包时没有指定主类

**解决：** 在 pom.xml 中添加：

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-shade-plugin</artifactId>
    <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                <mainClass>com.example.OrderProcessTask</mainClass>
            </transformer>
        </transformers>
    </configuration>
</plugin>
```

### Q3: 报错"连接 Kafka 失败"

**可能原因：**
1. Kafka 地址不对
2. Kafka 没启动
3. 网络不通

**检查步骤：**

```bash
# 1. 检查 Kafka 是否启动
ps aux | grep kafka

# 2. 测试连接
telnet 192.168.12.41 9092

# 3. 查看 Topic 是否存在
./kafka-topics.sh --list --bootstrap-server 192.168.12.41:9092
```

### Q4: 数据不累加，每次都是新值

**原因：** 没有正确使用 State

**解决：** 确保：
1. 使用了 `RichMapFunction` 而不是普通 `MapFunction`
2. 在 `open()` 方法中初始化 State
3. 用 `update()` 更新 State

### Q5: Scala 代码编译失败

**常见错误：** 类型不匹配

**解决：** 检查导入：

```scala
// 必须导入这个！
import org.apache.flink.api.scala._
```

---

## 📚 Scala 语法速查

### 变量定义

```scala
// 不可变变量（推荐）
val name = "张三"

// 可变变量
var age = 25
age = 26  // 可以修改
```

### 函数定义

```scala
// 完整写法
def add(a: Int, b: Int): Int = {
  return a + b
}

// 简化写法（省略 return 和大括号）
def add(a: Int, b: Int): Int = a + b

// 更简化（类型可推断）
def add(a: Int, b: Int) = a + b
```

### 匿名函数

```scala
// 完整写法
list.map(x => x * 2)

// 简化写法（省略参数名）
list.map(_ * 2)
```

### 模式匹配

```scala
// 匹配订单状态
order.status match {
  case "1003" => println("已取消")
  case "1005" => println("已完成")
  case "1006" => println("已退款")
  case _ => println("其他状态")
}
```

### Option 类型

```scala
// 安全地获取可能为 null 的值
val value = Option(maybeNull).getOrElse("默认值")
```

---

## 📚 学习路线建议

### 第 1 周：Scala 基础
- [ ] 变量和函数定义
- [ ] 集合操作（List、Map）
- [ ] 模式匹配
- [ ] Case Class

### 第 2 周：Flink 基础
- [x] 理解什么是流处理
- [x] 学会写简单的 WordCount
- [ ] 理解 DataStream、Transformation

### 第 3 周：核心概念
- [ ] 深入学习 State（状态）
- [ ] 深入学习 Window（窗口）
- [ ] 学习 Watermark（水位线）

### 第 4 周：连接器
- [ ] Kafka Connector
- [ ] Redis Connector
- [ ] JDBC Connector

### 第 5 周：实战项目
- [ ] 完成一个完整的实时计算项目
- [ ] 部署到 Flink 集群
- [ ] 学习监控和调优

---

## 🔗 学习资源

### 官方文档
- Flink 中文文档：https://nightlies.apache.org/flink/flink-docs-master-zh/
- Flink Scala API: https://nightlies.apache.org/flink/flink-docs-master/docs/dev/datastream/operators/overview/

### Scala 学习
- Scala 官方教程：https://docs.scala-lang.org/tour/basics.html
- 菜鸟教程 Scala: https://www.runoob.com/scala/scala-tutorial.html

### 视频教程
- B 站搜索"Flink Scala"
- 推荐 UP 主：尚硅谷、黑马程序员

### 书籍推荐
- 《Scala 编程》
- 《Flink 原理与实践》

### 社区
- Flink 中文社区：https://flink-china.org/
- Scala 中文社区：https://scala-lang.cn/

---

## 💡 下一步做什么？

学会这个教程后，你可以尝试：

### 1. 修改现有代码

```scala
// 添加新的统计指标
val avgStream = orderStream
  .map(order => ("avg_amount", order.amount))
  .keyBy(_._1)
  // 计算平均值（需要自己实现）
```

```scala
// 添加时间窗口
salesStream
  .keyBy(_.key)
  .timeWindow(Time.minutes(5))  // 每 5 分钟统计一次
  .sum("value")
```

### 2. 做自己的项目

- 实时统计网站访问量
- 实时监测设备温度
- 实时分析用户行为

### 3. 深入学习

- 学习 Flink SQL
- 学习 Flink CEP（复杂事件处理）
- 学习 Flink Table API

---

## 🎉 恭喜你完成了 Flink Scala 入门教程！

现在你已经掌握了：

✅ Scala 基础语法
✅ Flink 核心概念
✅ 完整的实时计算项目

**记住：**
- 多动手写代码
- 遇到问题多查文档
- 加入社区交流

有任何问题，欢迎在 GitHub 上提 Issue 或加入 Flink 社区交流！

*教程版本：1.0.0 | 最后更新：2026-03-11*
