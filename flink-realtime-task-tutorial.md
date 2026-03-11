# Flink 实时任务开发教程 - 订单数据处理实战

> 📚 基于 Flink 1.14.0 + Scala 的实时订单处理系统完整指南

![Flink](https://img.shields.io/badge/Flink-1.14.0-E6526F?style=for-the-badge&logo=apacheflink&logoColor=white)
![Scala](https://img.shields.io/badge/Scala-2.11.12-DC322F?style=for-the-badge&logo=scala&logoColor=white)
![Kafka](https://img.shields.io/badge/Kafka-2.x-231F20?style=for-the-badge&logo=apachekafka&logoColor=white)

---

## 📑 目录

- [项目概述](#项目概述)
- [POM 配置详解](#pom-配置详解)
- [代码架构解析](#代码架构解析)
- [核心功能实现](#核心功能实现)
- [部署与运行](#部署与运行)
- [常见问题解决](#常见问题解决)

---

## 项目概述

### 业务场景

这是一个**实时订单数据处理系统**，主要功能：

```
┌─────────────┐     ┌──────────────┐     ┌─────────────────┐
│   Kafka     │ ──→ │   Flink      │ ──→ │   Redis         │
│   order     │     │   实时计算    │     │   销售额/退款    │
│   Topic     │     │   任务        │     │   统计结果      │
└─────────────┘     └──────┬───────┘     └─────────────────┘
                           │
                           ↓
                    ┌─────────────┐
                    │   MySQL     │
                    │   取消订单   │
                    └─────────────┘
```

### 数据流处理逻辑

1. **数据源**：从 Kafka `order` Topic 消费订单数据
2. **数据解析**：解析 JSON 格式订单数据
3. **水位线分配**：处理乱序事件时间
4. **业务分流**：
   - 正常订单 → 计算销售总额 → 写入 Redis
   - 退款订单 (1006) → 计算退款总额 → 写入 Redis
   - 取消订单 (1003) → 写入 MySQL
5. **状态管理**：使用 ValueState 累加统计

---

## POM 配置详解

### 完整 POM 文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
 <modelVersion>4.0.0</modelVersion>

 <groupId>com.shtd</groupId>
 <artifactId>flink-realtime-tasks</artifactId>
 <version>1.0-SNAPSHOT</version>

 <properties>
  <maven.compiler.source>8</maven.compiler.source>
  <maven.compiler.target>8</maven.compiler.target>
  <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  <flink.version>1.14.0</flink.version>
  <scala.binary.version>2.11</scala.binary.version>
  <scala.version>2.11.12</scala.version>
  <log4j.version>2.14.1</log4j.version>
 </properties>

 <dependencies>
  <!-- Apache Flink 核心依赖 -->
  <dependency>
   <groupId>org.apache.flink</groupId>
   <artifactId>flink-scala_${scala.binary.version}</artifactId>
   <version>${flink.version}</version>
   <scope>provided</scope>
  </dependency>
  
  <dependency>
   <groupId>org.apache.flink</groupId>
   <artifactId>flink-streaming-scala_${scala.binary.version}</artifactId>
   <version>${flink.version}</version>
   <scope>provided</scope>
  </dependency>
  
  <dependency>
   <groupId>org.apache.flink</groupId>
   <artifactId>flink-clients_${scala.binary.version}</artifactId>
   <version>${flink.version}</version>
   <scope>provided</scope>
  </dependency>

  <!-- Scala 库 -->
  <dependency>
   <groupId>org.scala-lang</groupId>
   <artifactId>scala-library</artifactId>
   <version>${scala.version}</version>
   <scope>provided</scope>
  </dependency>

  <!-- Kafka Connector -->
  <dependency>
   <groupId>org.apache.flink</groupId>
   <artifactId>flink-connector-kafka_${scala.binary.version}</artifactId>
   <version>${flink.version}</version>
   <scope>compile</scope>
  </dependency>

  <!-- Redis Connector -->
  <dependency>
   <groupId>org.apache.bahir</groupId>
   <artifactId>flink-connector-redis_2.11</artifactId>
   <version>1.0</version>
   <scope>compile</scope>
   <exclusions>
    <exclusion>
     <groupId>org.apache.flink</groupId>
     <artifactId>flink-streaming-java_2.11</artifactId>
    </exclusion>
   </exclusions>
  </dependency>

  <!-- JDBC Connector for MySQL -->
  <dependency>
   <groupId>org.apache.flink</groupId>
   <artifactId>flink-connector-jdbc_${scala.binary.version}</artifactId>
   <version>${flink.version}</version>
   <scope>compile</scope>
  </dependency>

  <!-- MySQL Driver -->
  <dependency>
   <groupId>mysql</groupId>
   <artifactId>mysql-connector-java</artifactId>
   <version>8.0.28</version>
   <scope>compile</scope>
  </dependency>

  <!-- JSON Parser -->
  <dependency>
   <groupId>com.alibaba</groupId>
   <artifactId>fastjson</artifactId>
   <version>1.2.78</version>
   <scope>compile</scope>
  </dependency>

  <!-- HBase Client -->
  <dependency>
   <groupId>org.apache.hbase</groupId>
   <artifactId>hbase-client</artifactId>
   <version>2.2.3</version>
   <scope>compile</scope>
   <exclusions>
    <exclusion>
     <groupId>org.slf4j</groupId>
     <artifactId>slf4j-log4j12</artifactId>
    </exclusion>
    <exclusion>
     <groupId>log4j</groupId>
     <artifactId>log4j</artifactId>
    </exclusion>
   </exclusions>
  </dependency>

  <!-- Logging -->
  <dependency>
   <groupId>org.apache.logging.log4j</groupId>
   <artifactId>log4j-slf4j-impl</artifactId>
   <version>${log4j.version}</version>
   <scope>runtime</scope>
  </dependency>
  <dependency>
   <groupId>org.apache.logging.log4j</groupId>
   <artifactId>log4j-api</artifactId>
   <version>${log4j.version}</version>
   <scope>compile</scope>
  </dependency>
  <dependency>
   <groupId>org.apache.logging.log4j</groupId>
   <artifactId>log4j-core</artifactId>
   <version>${log4j.version}</version>
   <scope>compile</scope>
  </dependency>
 </dependencies>

 <build>
  <plugins>
   <!-- Scala Compiler Plugin -->
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
    <configuration>
     <scalaVersion>${scala.version}</scalaVersion>
    </configuration>
   </plugin>

   <!-- Java Compiler -->
   <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>3.8.1</version>
    <configuration>
     <source>${maven.compiler.source}</source>
     <target>${maven.compiler.target}</target>
    </configuration>
   </plugin>

   <!-- Shade Plugin for Fat Jar -->
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
      <configuration>
       <artifactSet>
        <excludes>
         <exclude>org.apache.flink:force-shading</exclude>
         <exclude>com.google.code.findbugs:jsr305</exclude>
         <exclude>org.slf4j:*</exclude>
         <exclude>org.apache.logging.log4j:*</exclude>
        </excludes>
       </artifactSet>
       <filters>
        <filter>
         <artifact>*:*</artifact>
         <excludes>
          <exclude>META-INF/*.SF</exclude>
          <exclude>META-INF/*.DSA</exclude>
          <exclude>META-INF/*.RSA</exclude>
         </excludes>
        </filter>
       </filters>
      </configuration>
     </execution>
    </executions>
   </plugin>
  </plugins>
 </build>
</project>
```

### 依赖配置说明

#### 1. Flink 核心依赖（provided）

```xml
<!-- 为什么用 provided？
Flink 集群运行时已经包含这些依赖，打包时不需要打入 JAR，避免冲突 -->
<dependency>
  <groupId>org.apache.flink</groupId>
  <artifactId>flink-scala_${scala.binary.version}</artifactId>
  <version>${flink.version}</version>
  <scope>provided</scope>
</dependency>
```

| 依赖 | 用途 | Scope |
|------|------|-------|
| `flink-scala` | Scala API 支持 | provided |
| `flink-streaming-scala` | 流处理 API | provided |
| `flink-clients` | 客户端提交任务 | provided |
| `scala-library` | Scala 运行时库 | provided |

#### 2. 连接器依赖（compile）

```xml
<!-- Kafka Connector - 数据源 -->
<dependency>
  <groupId>org.apache.flink</groupId>
  <artifactId>flink-connector-kafka_${scala.binary.version}</artifactId>
  <version>${flink.version}</version>
  <scope>compile</scope>
</dependency>

<!-- Redis Connector - 数据输出 -->
<dependency>
  <groupId>org.apache.bahir</groupId>
  <artifactId>flink-connector-redis_2.11</artifactId>
  <version>1.0</version>
</dependency>

<!-- JDBC Connector - MySQL 输出 -->
<dependency>
  <groupId>org.apache.flink</groupId>
  <artifactId>flink-connector-jdbc_${scala.binary.version}</artifactId>
  <version>${flink.version}</version>
</dependency>
```

#### 3. 版本兼容性

| 组件 | 版本 | 注意事项 |
|------|------|----------|
| Flink | 1.14.0 | 主流稳定版本 |
| Scala | 2.11.12 | 必须与 Flink 编译版本一致 |
| Kafka Connector | 1.14.0 | 必须与 Flink 版本一致 |
| MySQL Driver | 8.0.28 | 兼容 MySQL 5.7/8.0 |
| FastJSON | 1.2.78 | 注意安全性，生产建议用 Jackson |

---

## 代码架构解析

### 项目结构

```
src/main/scala/com/shtd/flink/
├── Task1.scala              # 主任务入口
├── model/
│   └── OrderData.scala      # 数据模型（可选）
├── function/
│   ├── OrderParseFunction.scala  # 解析函数
│   └── OrderProcessFunction.scala # 处理函数
└── sink/
    ├── RedisSinkFunction.scala   # Redis 输出
    └── MysqlSinkFunction.scala   # MySQL 输出
```

### 核心代码解析

#### 1. 数据模型定义

```scala
case class OrderData(
  id: Long,                    // 订单 ID
  consignee: String,           // 收货人
  consignee_tel: String,       // 联系电话
  final_total_amount: Double,  // 订单金额
  order_status: String,        // 订单状态
  create_time: String,         // 创建时间
  operate_time: String,        // 操作时间
  feight_fee: Double,          // 运费
  event_time_ts: Long          // 事件时间戳（用于水位线）
)
```

#### 2. 执行环境配置

```scala
val env = StreamExecutionEnvironment.getExecutionEnvironment
env.setParallelism(1)  // 设置并行度

// 生产环境建议配置：
// env.setParallelism(4)
// env.enableCheckpointing(5000)  // 5 秒检查点
// env.getCheckpointConfig.setMinPauseBetweenCheckpoints(3000)
```

#### 3. Kafka 数据源

```scala
val properties = new Properties()
properties.setProperty("bootstrap.servers", "192.168.12.41:9092")
properties.setProperty("group.id", "flink-task1-group")

val stream = env.addSource(
  new FlinkKafkaConsumer[String](
    "order",                        // Topic 名称
    new SimpleStringSchema(),       // 反序列化
    properties
  ).setStartFromLatest()            // 从最新数据开始
)
```

**Kafka 配置参数说明：**

| 参数 | 说明 | 示例值 |
|------|------|--------|
| `bootstrap.servers` | Kafka 集群地址 | 192.168.12.41:9092 |
| `group.id` | 消费者组 ID | flink-task1-group |
| `auto.offset.reset` | 偏移量重置策略 | latest/earliest |

#### 4. JSON 解析与时间处理

```scala
val parsedStream = stream.map(jsonStr => {
  try {
    val jsonObj = JSON.parseObject(jsonStr)
    
    // 提取字段
    val id = jsonObj.getLongValue("id")
    val consignee = jsonObj.getString("consignee")
    // ... 其他字段
    
    // 时间转换
    val formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss")
    val createTs = if (create_time != null && create_time.nonEmpty) 
      LocalDateTime.parse(create_time, formatter)
        .atZone(ZoneId.systemDefault())
        .toInstant
        .toEpochMilli 
    else 0L
    
    // 取较晚的时间作为事件时间
    val eventTs = math.max(createTs, operateTs)
    
    OrderData(id, consignee, consignee_tel, final_total_amount, 
              order_status, create_time, operate_time, feight_fee, eventTs)
  } catch { 
    case _: Exception => null  // 解析失败返回 null
  }
}).filter(_ != null)  // 过滤掉解析失败的记录
```

#### 5. 水位线（Watermark）策略

```scala
.assignTimestampsAndWatermarks(
  WatermarkStrategy
    .forBoundedOutOfOrderness[OrderData](Duration.ofSeconds(5))  // 允许 5 秒乱序
    .withTimestampAssigner(new SerializableTimestampAssigner[OrderData] {
      override def extractTimestamp(element: OrderData, recordTimestamp: Long): Long = 
        element.event_time_ts  // 使用事件时间字段
    })
)
```

**水位线参数调优：**

| 场景 | 乱序程度 | 建议配置 |
|------|----------|----------|
| 网络稳定 | < 1 秒 | `Duration.ofSeconds(1)` |
| 一般场景 | 1-5 秒 | `Duration.ofSeconds(5)` |
| 网络波动大 | 5-10 秒 | `Duration.ofSeconds(10)` |

#### 6. 侧输出流分流

```scala
// 定义侧输出标签
val refundTag = OutputTag[OrderData]("refund-stream")   // 退款订单
val cancelTag = OutputTag[OrderData]("cancel-stream")   // 取消订单

// 业务分流处理
val processedStream = parsedStream.process(
  new ProcessFunction[OrderData, (String, Double)] {
    override def processElement(
      value: OrderData, 
      ctx: Context, 
      out: Collector[(String, Double)]
    ): Unit = {
      // 正常订单（非退款、非取消）
      if (value.order_status != "1003" && 
          value.order_status != "1005" && 
          value.order_status != "1006") {
        out.collect(("totalprice", value.final_total_amount))
      }
      
      // 退款订单 → 侧输出
      if (value.order_status == "1006") 
        ctx.output(refundTag, value)
      
      // 取消订单 → 侧输出
      if (value.order_status == "1003") 
        ctx.output(cancelTag, value)
    }
  }
)
```

**订单状态码说明：**

| 状态码 | 含义 | 处理逻辑 |
|--------|------|----------|
| 1003 | 已取消 | 写入 MySQL |
| 1005 | 已完成 | 计入销售额 |
| 1006 | 已退款 | 计入退款额 |
| 其他 | 正常订单 | 计入销售额 |

#### 7. 状态管理（ValueState）

```scala
// 销售总额累加
val totalPriceStream = processedStream
  .keyBy(_._1)  // 按 key 分组
  .map(new RichMapFunction[(String, Double), (String, Double)] {
    var sumState: ValueState[Double] = _
    
    override def open(parameters: Configuration): Unit = {
      // 初始化状态
      sumState = getRuntimeContext.getState(
        new ValueStateDescriptor[Double](
          "totalPriceState",           // 状态名称
          classOf[Double]              // 状态类型
        )
      )
    }
    
    override def map(value: (String, Double)): (String, Double) = {
      // 读取当前状态值
      val currentSum = if (sumState.value() == null) 0.0 else sumState.value()
      // 累加
      val newSum = currentSum + value._2
      // 更新状态
      sumState.update(newSum)
      // 输出结果
      (value._1, newSum)
    }
  })
```

**状态类型对比：**

| 状态类型 | 用途 | 示例 |
|----------|------|------|
| `ValueState[T]` | 单个值 | 累加和、计数器 |
| `ListState[T]` | 列表 | 最近 N 条记录 |
| `MapState[K,V]` | 键值对 | 用户画像 |
| `ReducingState[T]` | 归约 | 自定义聚合 |

#### 8. Redis Sink

```scala
// Redis 配置
val redisConfig = new FlinkJedisPoolConfig.Builder()
  .setHost("192.168.12.41")
  .setPort(6379)
  .build()

// 自定义 Redis Mapper
class RedisSetMapper extends RedisMapper[(String, Double)] {
  override def getCommandDescription: RedisCommandDescription = 
    new RedisCommandDescription(RedisCommand.SET)  // SET 命令
  
  override def getKeyFromData(data: (String, Double)): String = 
    data._1  // key: totalprice / totalrefundordercount
  
  override def getValueFromData(data: (String, Double)): String = 
    f"${data._2}%.2f"  // value: 保留 2 位小数
}

// 添加 Redis Sink
totalPriceStream.addSink(
  new RedisSink[(String, Double)](redisConfig, new RedisSetMapper)
)
```

**Redis 配置参数：**

```scala
val redisConfig = new FlinkJedisPoolConfig.Builder()
  .setHost("192.168.12.41")      // Redis 主机
  .setPort(6379)                 // Redis 端口
  .setConnectionTimeout(5000)    // 连接超时 (ms)
  .setSoTimeout(5000)            // 读取超时 (ms)
  .setMaxTotal(50)               // 最大连接数
  .setMaxIdle(10)                // 最大空闲连接
  .setMinIdle(5)                 // 最小空闲连接
  .setPassword("your-password")  // 密码（如有）
  .setDatabase(0)                // 数据库索引
  .build()
```

#### 9. MySQL JDBC Sink

```scala
// MySQL 连接配置
val jdbcOptions = new JdbcConnectionOptions.JdbcConnectionOptionsBuilder()
  .withUrl("jdbc:mysql://192.168.12.41:3306/shtd_result?useSSL=false&characterEncoding=utf8")
  .withDriverName("com.mysql.cj.jdbc.Driver")
  .withUsername("root")
  .withPassword("123456")
  .build()

// 取消订单写入 MySQL
cancelStream.addSink(
  JdbcSink.sink(
    "INSERT INTO order_info (id, consignee, consignee_tel, final_total_amount, feight_fee) " +
    "VALUES (?, ?, ?, ?, ?) " +
    "ON DUPLICATE KEY UPDATE consignee=VALUES(consignee)",  // 主键冲突时更新
    
    new JdbcStatementBuilder[OrderData] {
      override def accept(ps: PreparedStatement, t: OrderData): Unit = {
        ps.setLong(1, t.id)
        ps.setString(2, t.consignee)
        ps.setString(3, t.consignee_tel)
        ps.setDouble(4, t.final_total_amount)
        ps.setDouble(5, t.feight_fee)
      }
    },
    jdbcOptions
  )
)
```

**MySQL 表结构：**

```sql
CREATE TABLE order_info (
  id BIGINT PRIMARY KEY,
  consignee VARCHAR(100),
  consignee_tel VARCHAR(20),
  final_total_amount DECIMAL(10,2),
  feight_fee DECIMAL(10,2),
  create_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  update_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

---

## 部署与运行

### 1. 本地开发环境运行

```bash
# 1. 克隆项目
git clone https://github.com/sjojoun/linux-basic-handbook.git
cd maven-tutorial

# 2. 编译打包
mvn clean package

# 3. 本地运行（IDEA 或 Eclipse）
# 直接运行 Task1.main() 方法
```

### 2. Flink 集群部署

```bash
# 1. 上传 JAR 到 Flink 集群
scp target/flink-realtime-tasks-1.0-SNAPSHOT.jar flink@node1:/opt/flink/jobs/

# 2. 提交任务
./bin/flink run \
  -c com.shtd.flink.Task1 \
  -p 4 \
  /opt/flink/jobs/flink-realtime-tasks-1.0-SNAPSHOT.jar

# 3. 查看任务状态
./bin/flink list

# 4. 取消任务
./bin/flink cancel <job-id>
```

### 3. Flink Web UI

访问：http://flink-node1:8081

- **Jobs** → 查看任务状态
- **Task Managers** → 查看资源使用
- **Checkpoints** → 查看检查点

### 4. 生产环境配置建议

```scala
// 启用检查点
env.enableCheckpointing(5000)  // 5 秒一次
env.getCheckpointConfig.setCheckpointTimeout(60000)  // 超时 1 分钟
env.getCheckpointConfig.setMinPauseBetweenCheckpoints(3000)  // 最小间隔 3 秒
env.getCheckpointConfig.setMaxConcurrentCheckpoints(1)  // 并发 1 个

// 故障恢复策略
env.setRestartStrategy(
  RestartStrategies.fixedDelayRestart(
    3,  // 重试 3 次
    Time.of(10, TimeUnit.SECONDS)  // 每次间隔 10 秒
  )
)

// 设置状态后端（推荐 RocksDB）
env.setStateBackend(new RocksDBStateBackend("hdfs://namenode:8020/flink/checkpoints"))
```

---

## 常见问题解决

### 1. 依赖冲突

**问题：** `ClassNotFound: org.apache.flink.streaming.connectors.kafka.FlinkKafkaConsumer`

**解决：** 检查 Flink 版本和 Connector 版本是否一致

```xml
<!-- 确保版本一致 -->
<flink.version>1.14.0</flink.version>
<dependency>
  <groupId>org.apache.flink</groupId>
  <artifactId>flink-connector-kafka_${scala.binary.version}</artifactId>
  <version>${flink.version}</version>
</dependency>
```

### 2. Kafka 连接失败

**问题：** `LeaderNotAvailableException`

**解决：** 检查 Kafka 配置

```bash
# 检查 Kafka 是否可访问
telnet 192.168.12.41 9092

# 检查 Topic 是否存在
./bin/kafka-topics.sh --list --bootstrap-server 192.168.12.41:9092
```

### 3. Redis 连接超时

**问题：** `JedisConnectionException: Could not get a resource from the pool`

**解决：** 增加连接池配置

```scala
val redisConfig = new FlinkJedisPoolConfig.Builder()
  .setHost("192.168.12.41")
  .setPort(6379)
  .setMaxTotal(50)  // 增加最大连接数
  .setConnectionTimeout(10000)  // 增加超时时间
  .build()
```

### 4. 水位线延迟导致数据不输出

**问题：** 结果长时间不更新

**解决：** 添加定时输出或调整水位线

```scala
// 方案 1：减少乱序时间
WatermarkStrategy.forBoundedOutOfOrderness(Duration.ofSeconds(1))

// 方案 2：添加定时触发器（使用 ProcessFunction）
```

### 5. 状态过大导致 OOM

**问题：** `java.lang.OutOfMemoryError: Java heap space`

**解决：**

```scala
// 1. 使用 RocksDB 状态后端
env.setStateBackend(new RocksDBStateBackend("hdfs://path"))

// 2. 配置状态清理 TTL
val stateDesc = new ValueStateDescriptor[Double]("totalPriceState", classOf[Double])
stateDesc.setStateTtlConfig(
  new StateTtlConfig.Builder(Time.hours(24))
    .setUpdateType(StateTtlConfig.UpdateType.OnCreateAndWrite)
    .setStateVisibility(StateTtlConfig.StateVisibility.NeverReturnExpired)
    .build()
)
```

### 6. MySQL 写入失败

**问题：** `Communications link failure`

**解决：**

```scala
// 1. 检查 MySQL 连接
val jdbcOptions = new JdbcConnectionOptions.JdbcConnectionOptionsBuilder()
  .withUrl("jdbc:mysql://192.168.12.41:3306/shtd_result?useSSL=false&characterEncoding=utf8&connectTimeout=30000&socketTimeout=30000")
  // ...
  .build()

// 2. 检查 MySQL 用户权限
GRANT ALL PRIVILEGES ON shtd_result.* TO 'root'@'%' IDENTIFIED BY '123456';
FLUSH PRIVILEGES;
```

---

## 扩展与优化

### 1. 添加监控指标

```scala
// 使用 Flink Metrics
class RichMapFunctionWithMetrics extends RichMapFunction[(String, Double), (String, Double)] {
  @transient private var counter: Counter = _
  @transient private var gauge: Gauge[Double] = _
  
  override def open(parameters: Configuration): Unit = {
    counter = getRuntimeContext.getMetricGroup.counter("total_records")
    gauge = getRuntimeContext.getMetricGroup.gauge("current_sum", new Gauge[Double] {
      override def getValue: Double = sumState.value()
    })
  }
}
```

### 2. 添加数据质量检查

```scala
// 过滤无效数据
val cleanStream = parsedStream.filter { order =>
  order.id > 0 && 
  order.final_total_amount >= 0 && 
  order.consignee_tel != null &&
  order.consignee_tel.matches("^1[3-9]\\d{9}$")  // 手机号验证
}
```

### 3. 多版本兼容

```scala
// 适配不同 Flink 版本
val flinkVersion = System.getProperty("flink.version", "1.14.0")
val connectorVersion = flinkVersion match {
  case v if v.startsWith("1.14") => "1.14.0"
  case v if v.startsWith("1.13") => "1.13.2"
  case _ => "1.14.0"
}
```

---

## 学习资源

- **Flink 官方文档**: https://flink.apache.org/
- **Flink 中文社区**: https://flink-china.org/
- **Kafka 官方文档**: https://kafka.apache.org/
- **Redis 官方文档**: https://redis.io/

---

*教程版本：1.0.0 | 最后更新：2026-03-11*
