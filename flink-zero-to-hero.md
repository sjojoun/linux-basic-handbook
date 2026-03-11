# Flink 零基础入门教程 - 手把手教你写第一个实时任务

> 📚 完全零基础友好！不需要任何 Flink 经验，跟着步骤做就能学会

![Flink](https://img.shields.io/badge/Flink-入门教程-E6526F?style=for-the-badge)
![难度](https://img.shields.io/badge/难度-⭐⭐-yellow?style=for-the-badge)
![适合人群](https://img.shields.io/badge/适合人群-零基础-green?style=for-the-badge)

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

### 实际应用场景

| 公司 | 用 Flink 做什么 |
|------|----------------|
| 淘宝 | 双 11 实时成交额大屏 |
| 滴滴 | 实时显示附近有多少车 |
| 抖音 | 实时统计直播间人数 |
| 银行 | 实时检测异常交易 |

---

## 🛠️ 第一步：准备工作

### 需要安装什么？

**最少只需要 2 个软件：**

1. **JDK 8** - 运行 Java 程序的环境
2. **Maven** - 自动下载项目需要的库

**可选（有更好）：**
- IntelliJ IDEA - 写代码的工具（比记事本好用 100 倍）
- Kafka - 如果要从消息队列读取数据
- Flink 集群 - 如果要运行大规模任务

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

## 📁 第二步：创建项目

### 方法 1：用 IDEA（推荐）

1. 打开 IDEA
2. 点击 `New Project`
3. 左边选 `Maven`
4. 点击 `Next`
5. 填写：
   - GroupId: `com.example`
   - ArtifactId: `flink-demo`
6. 点击 `Finish`

### 方法 2：用命令行

```bash
# 创建一个文件夹
mkdir flink-demo
cd flink-demo

# 创建 pom.xml 文件
```

### 创建 pom.xml 文件

在项目文件夹里创建 `pom.xml` 文件，复制下面的内容：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project>
    <!-- 项目基本信息 -->
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>flink-demo</artifactId>
    <version>1.0-SNAPSHOT</version>
    
    <!-- 配置 Java 版本 -->
    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <flink.version>1.14.0</flink.version>
        <scala.version>2.11.12</scala.version>
    </properties>
    
    <!-- 需要的库 -->
    <dependencies>
        <!-- Flink 核心 -->
        <dependency>
            <groupId>org.apache.flink</groupId>
            <artifactId>flink-streaming-java_${scala.version}</artifactId>
            <version>${flink.version}</version>
        </dependency>
        
        <!-- Flink 客户端 -->
        <dependency>
            <groupId>org.apache.flink</groupId>
            <artifactId>flink-clients_${scala.version}</artifactId>
            <version>${flink.version}</version>
        </dependency>
        
        <!-- Kafka 连接器 -->
        <dependency>
            <groupId>org.apache.flink</groupId>
            <artifactId>flink-connector-kafka_${scala.version}</artifactId>
            <version>${flink.version}</version>
        </dependency>
        
        <!-- Redis 连接器 -->
        <dependency>
            <groupId>org.apache.bahir</groupId>
            <artifactId>flink-connector-redis_2.11</artifactId>
            <version>1.0</version>
        </dependency>
        
        <!-- MySQL 连接器 -->
        <dependency>
            <groupId>org.apache.flink</groupId>
            <artifactId>flink-connector-jdbc_${scala.version}</artifactId>
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
    
    <!-- 打包配置 -->
    <build>
        <plugins>
            <!-- Java 编译插件 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <source>8</source>
                    <target>8</target>
                </configuration>
            </plugin>
            
            <!-- 打包插件（把所有依赖打成一个包） -->
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

**💡 通俗解释 pom.xml：**

```xml
<!-- 这就像你的购物清单 -->
<dependencies>
    <!-- 需要 Flink -->
    <dependency>Flink</dependency>
    <!-- 需要 Kafka -->
    <dependency>Kafka</dependency>
    <!-- 需要 Redis -->
    <dependency>Redis</dependency>
    <!-- 需要 MySQL -->
    <dependency>MySQL</dependency>
</dependencies>

<!-- Maven 会自动帮你下载这些东西 -->
```

---

## 💻 第三步：写第一个 Flink 程序

### 最简单的例子：单词计数

**任务：** 数一数输入的句子中每个单词出现了几次

**输入：** `hello world hello flink`

**输出：** 
- hello: 2 次
- world: 1 次
- flink: 1 次

### 完整代码

在 `src/main/java` 目录下创建 `WordCount.java`：

```java
import org.apache.flink.api.common.functions.FlatMapFunction;
import org.apache.flink.api.java.tuple.Tuple2;
import org.apache.flink.streaming.api.datastream.DataStream;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.util.Collector;

public class WordCount {
    
    public static void main(String[] args) throws Exception {
        // 1. 创建执行环境
        // 想象这是 Flink 的"工作台"
        final StreamExecutionEnvironment env = 
            StreamExecutionEnvironment.getExecutionEnvironment();
        
        // 2. 创建数据源
        // 这里用简单的文本数据，实际可以用 Kafka、文件等
        DataStream<String> textStream = env.fromElements(
            "hello world hello flink",
            "flink is awesome",
            "hello flink world"
        );
        
        // 3. 处理数据
        // 把句子拆成单词，然后计数
        DataStream<Tuple2<String, Integer>> result = textStream
            // 第一步：把句子拆成单词
            .flatMap(new FlatMapFunction<String, Tuple2<String, Integer>>() {
                @Override
                public void flatMap(String sentence, Collector<Tuple2<String, Integer>> out) {
                    // 按空格拆分
                    String[] words = sentence.split(" ");
                    // 输出每个单词
                    for (String word : words) {
                        out.collect(new Tuple2<>(word, 1));
                    }
                }
            })
            // 第二步：按单词分组
            .keyBy(value -> value.f0)  // f0 表示第一个字段（单词）
            // 第三步：累加计数
            .sum(1);  // 1 表示第二个字段（计数）
        
        // 4. 打印结果
        result.print();
        
        // 5. 执行任务
        env.execute("WordCount Demo");
    }
}
```

### 运行代码

**在 IDEA 中：**
1. 右键点击 `WordCount.java`
2. 选择 `Run 'WordCount.main()'`
3. 看控制台输出

**在命令行：**
```bash
# 编译
mvn clean compile

# 运行
mvn exec:java -Dexec.mainClass="WordCount"
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

## 🎯 第四步：理解核心概念

### 概念 1：DataStream（数据流）

**通俗理解：** 一条源源不断的数据河流

```java
// 创建数据流
DataStream<String> stream = env.fromElements("数据 1", "数据 2", "数据 3");

// 就像：
// 河流 → 数据 1 → 数据 2 → 数据 3 → ...
```

### 概念 2：Transformation（转换）

**通俗理解：** 对数据流进行加工

```java
// 常见的转换操作：

// 1. map - 一对一转换
// 输入： "123" → 输出：123 (数字)
stream.map(s -> Integer.parseInt(s));

// 2. filter - 过滤
// 只保留包含"hello"的数据
stream.filter(s -> s.contains("hello"));

// 3. flatMap - 一对多转换
// 输入： "a b c" → 输出："a", "b", "c"
stream.flatMap((s, out) -> {
    for (String word : s.split(" ")) {
        out.collect(word);
    }
});

// 4. keyBy - 分组
// 按第一个字段分组
stream.keyBy(value -> value.f0);

// 5. sum - 累加
// 对第二个字段累加
stream.sum(1);
```

### 概念 3：State（状态）

**通俗理解：** Flink 的"记忆力"

```java
// 没有状态：记不住之前发生了什么
// 输入：1, 2, 3 → 输出：1, 2, 3（只是原样输出）

// 有状态：能记住之前的数据
// 输入：1, 2, 3 → 输出：1, 3, 6（累加结果）
```

**代码示例：**

```java
// 使用 ValueState 记住累加和
stream.keyBy(key)
    .map(new RichMapFunction<Data, Data>() {
        private ValueState<Integer> sumState;
        
        @Override
        public void open(Configuration parameters) {
            // 初始化状态（就像准备一个小本本）
            sumState = getRuntimeContext.getState(
                new ValueStateDescriptor<>("sum", Integer.class)
            );
        }
        
        @Override
        public Data map(Data value) {
            // 读取之前的和
            int currentSum = sumState.value();
            if (currentSum == 0) currentSum = 0;
            
            // 累加
            int newSum = currentSum + value.count;
            
            // 更新状态（记在小本本上）
            sumState.update(newSum);
            
            value.sum = newSum;
            return value;
        }
    });
```

### 概念 4：Window（窗口）

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

```java
// 每 5 分钟统计一次
stream
    .keyBy(value -> value.userId)
    .timeWindow(Time.minutes(5))  // 5 分钟窗口
    .sum("amount");  // 累加金额
```

---

## 📦 第五步：实战 - 订单处理系统

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

创建 `OrderProcessTask.java`：

```java
import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONObject;
import org.apache.flink.api.common.eventtime.WatermarkStrategy;
import org.apache.flink.api.common.functions.RichMapFunction;
import org.apache.flink.api.common.serialization.SimpleStringSchema;
import org.apache.flink.api.common.state.ValueState;
import org.apache.flink.api.common.state.ValueStateDescriptor;
import org.apache.flink.configuration.Configuration;
import org.apache.flink.connector.jdbc.JdbcConnectionOptions;
import org.apache.flink.connector.jdbc.JdbcSink;
import org.apache.flink.connector.kafka.FlinkKafkaConsumer;
import org.apache.flink.streaming.api.datastream.DataStream;
import org.apache.flink.streaming.api.environment.StreamExecutionEnvironment;
import org.apache.flink.streaming.connectors.redis.RedisSink;
import org.apache.flink.streaming.connectors.redis.common.config.FlinkJedisPoolConfig;
import org.apache.flink.streaming.connectors.redis.common.mapper.RedisCommand;
import org.apache.flink.streaming.connectors.redis.common.mapper.RedisCommandDescription;
import org.apache.flink.streaming.connectors.redis.common.mapper.RedisMapper;
import org.apache.flink.util.Collector;

import java.sql.PreparedStatement;
import java.time.format.DateTimeFormatter;
import java.time.LocalDateTime;
import java.time.ZoneId;
import java.util.Properties;

public class OrderProcessTask {
    
    // 订单数据类
    public static class Order {
        public Long id;
        public String consignee;
        public String consignee_tel;
        public Double amount;
        public String status;
        public String createTime;
        public String operateTime;
        public Double freightFee;
        public Long eventTime;  // 事件时间
        
        public Order() {}
    }
    
    // 统计结果类
    public static class StatResult {
        public String key;
        public Double value;
        
        public StatResult(String key, Double value) {
            this.key = key;
            this.value = value;
        }
    }
    
    public static void main(String[] args) throws Exception {
        // ========== 第一步：创建环境 ==========
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.setParallelism(1);  // 设置并行度为 1（简单起见）
        
        // ========== 第二步：配置 Kafka 数据源 ==========
        Properties props = new Properties();
        props.setProperty("bootstrap.servers", "192.168.12.41:9092");  // Kafka 地址
        props.setProperty("group.id", "flink-order-group");  // 消费者组
        
        // 创建 Kafka 数据流
        DataStream<String> kafkaStream = env.addSource(
            new FlinkKafkaConsumer<>(
                "order",  // Topic 名称
                new SimpleStringSchema(),  // 字符串格式
                props
            )
        );
        
        // ========== 第三步：解析 JSON 数据 ==========
        DataStream<Order> orderStream = kafkaStream
            .map(jsonStr -> {
                try {
                    JSONObject json = JSON.parseObject(jsonStr);
                    Order order = new Order();
                    
                    // 提取字段
                    order.id = json.getLong("id");
                    order.consignee = json.getString("consignee");
                    order.consignee_tel = json.getString("consignee_tel");
                    order.amount = json.getDouble("final_total_amount");
                    order.status = json.getString("order_status");
                    order.createTime = json.getString("create_time");
                    order.operateTime = json.getString("operate_time");
                    order.freightFee = json.getDouble("feight_fee");
                    
                    // 转换时间为时间戳
                    DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
                    long createTs = LocalDateTime.parse(order.createTime, formatter)
                        .atZone(ZoneId.systemDefault())
                        .toInstant()
                        .toEpochMilli();
                    long operateTs = LocalDateTime.parse(order.operateTime, formatter)
                        .atZone(ZoneId.systemDefault())
                        .toInstant()
                        .toEpochMilli();
                    
                    // 取较晚的时间作为事件时间
                    order.eventTime = Math.max(createTs, operateTs);
                    
                    return order;
                } catch (Exception e) {
                    return null;  // 解析失败返回 null
                }
            })
            .filter(order -> order != null);  // 过滤掉解析失败的
        
        // ========== 第四步：业务分流处理 ==========
        
        // 4.1 正常订单 → 计算销售额
        DataStream<StatResult> salesStream = orderStream
            .filter(order -> !order.status.equals("1003") && 
                           !order.status.equals("1005") && 
                           !order.status.equals("1006"))
            .map(order -> new StatResult("total_sales", order.amount))
            .keyBy(result -> result.key)
            .map(new RichMapFunction<StatResult, StatResult>() {
                private ValueState<Double> sumState;
                
                @Override
                public void open(Configuration parameters) {
                    sumState = getRuntimeContext().getState(
                        new ValueStateDescriptor<>("sales_sum", Double.class)
                    );
                }
                
                @Override
                public StatResult map(StatResult value) {
                    Double current = sumState.value();
                    if (current == null) current = 0.0;
                    
                    Double newSum = current + value.value;
                    sumState.update(newSum);
                    
                    return new StatResult(value.key, newSum);
                }
            });
        
        // 4.2 退款订单 → 计算退款额
        DataStream<StatResult> refundStream = orderStream
            .filter(order -> order.status.equals("1006"))
            .map(order -> new StatResult("total_refund", order.amount))
            .keyBy(result -> result.key)
            .map(new RichMapFunction<StatResult, StatResult>() {
                private ValueState<Double> sumState;
                
                @Override
                public void open(Configuration parameters) {
                    sumState = getRuntimeContext().getState(
                        new ValueStateDescriptor<>("refund_sum", Double.class)
                    );
                }
                
                @Override
                public StatResult map(StatResult value) {
                    Double current = sumState.value();
                    if (current == null) current = 0.0;
                    
                    Double newSum = current + value.value;
                    sumState.update(newSum);
                    
                    return new StatResult(value.key, newSum);
                }
            });
        
        // 4.3 取消订单 → 准备写入 MySQL
        DataStream<Order> cancelStream = orderStream
            .filter(order -> order.status.equals("1003"));
        
        // ========== 第五步：配置输出 ==========
        
        // 5.1 Redis 配置
        FlinkJedisPoolConfig redisConfig = new FlinkJedisPoolConfig.Builder()
            .setHost("192.168.12.41")
            .setPort(6379)
            .build();
        
        // 5.2 写入 Redis
        salesStream.addSink(new RedisSink<>(redisConfig, new RedisMapper<StatResult>() {
            @Override
            public RedisCommandDescription getCommandDescription() {
                return new RedisCommandDescription(RedisCommand.SET);
            }
            
            @Override
            public String getKeyFromData(StatResult data) {
                return data.key;
            }
            
            @Override
            public String getValueFromData(StatResult data) {
                return String.format("%.2f", data.value);
            }
        }));
        
        refundStream.addSink(new RedisSink<>(redisConfig, new RedisMapper<StatResult>() {
            @Override
            public RedisCommandDescription getCommandDescription() {
                return new RedisCommandDescription(RedisCommand.SET);
            }
            
            @Override
            public String getKeyFromData(StatResult data) {
                return data.key;
            }
            
            @Override
            public String getValueFromData(StatResult data) {
                return String.format("%.2f", data.value);
            }
        }));
        
        // 5.3 写入 MySQL
        cancelStream.addSink(JdbcSink.sink(
            "INSERT INTO order_cancel (id, consignee, consignee_tel, amount, freight_fee) " +
            "VALUES (?, ?, ?, ?, ?) " +
            "ON DUPLICATE KEY UPDATE consignee=VALUES(consignee)",
            
            (PreparedStatement ps, Order order) -> {
                ps.setLong(1, order.id);
                ps.setString(2, order.consignee);
                ps.setString(3, order.consignee_tel);
                ps.setDouble(4, order.amount);
                ps.setDouble(5, order.freightFee);
            },
            
            new JdbcConnectionOptions.JdbcConnectionOptionsBuilder()
                .withUrl("jdbc:mysql://192.168.12.41:3306/shtd_result?useSSL=false&characterEncoding=utf8")
                .withDriverName("com.mysql.cj.jdbc.Driver")
                .withUsername("root")
                .withPassword("123456")
                .build()
        ));
        
        // ========== 第六步：启动任务 ==========
        System.out.println("任务已启动...");
        env.execute("订单处理任务");
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
java -jar target/flink-demo-1.0-SNAPSHOT.jar
```

### 提交到 Flink 集群（生产环境）

```bash
# 1. 启动 Flink 集群
cd /opt/flink
./bin/start-cluster.sh

# 2. 提交任务
./bin/flink run \
  -c OrderProcessTask \
  -p 4 \
  target/flink-demo-1.0-SNAPSHOT.jar

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

### Q1: 报错"找不到主类"

**原因：** 打包时没有指定主类

**解决：** 在 pom.xml 中添加：

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-shade-plugin</artifactId>
    <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                <mainClass>OrderProcessTask</mainClass>
            </transformer>
        </transformers>
    </configuration>
</plugin>
```

### Q2: 报错"连接 Kafka 失败"

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

### Q3: 报错"连接 Redis 失败"

**检查步骤：**

```bash
# 1. 检查 Redis 是否启动
redis-cli ping
# 应该返回 PONG

# 2. 测试连接
redis-cli -h 192.168.12.41 -p 6379
```

### Q4: 数据不累加，每次都是新值

**原因：** 没有正确使用 State

**解决：** 确保：
1. 使用了 `RichMapFunction` 而不是普通 `MapFunction`
2. 在 `open()` 方法中初始化 State
3. 用 `update()` 更新 State

### Q5: 任务运行很慢

**优化建议：**

```java
// 1. 增加并行度
env.setParallelism(4);

// 2. 启用检查点
env.enableCheckpointing(5000);  // 5 秒一次

// 3. 使用 RocksDB 状态后端（大数据量时）
env.setStateBackend(new RocksDBStateBackend("hdfs://path"));
```

---

## 📚 学习路线建议

### 第 1 周：基础入门
- [x] 理解什么是流处理
- [x] 学会写简单的 WordCount
- [ ] 理解 DataStream、Transformation

### 第 2 周：核心概念
- [ ] 深入学习 State（状态）
- [ ] 深入学习 Window（窗口）
- [ ] 学习 Watermark（水位线）

### 第 3 周：连接器
- [ ] Kafka Connector
- [ ] Redis Connector
- [ ] JDBC Connector

### 第 4 周：实战项目
- [ ] 完成一个完整的实时计算项目
- [ ] 部署到 Flink 集群
- [ ] 学习监控和调优

---

## 🔗 学习资源

### 官方文档
- Flink 中文文档：https://nightlies.apache.org/flink/flink-docs-master-zh/
- Flink GitHub: https://github.com/apache/flink

### 视频教程
- B 站搜索"Flink 入门"
- 推荐 UP 主：尚硅谷、黑马程序员

### 书籍推荐
- 《Flink 原理与实践》
- 《流式计算：Flink 原理与实践》

### 社区
- Flink 中文社区：https://flink-china.org/
- Stack Overflow: https://stackoverflow.com/questions/tagged/apache-flink

---

## 💡 下一步做什么？

学会这个教程后，你可以尝试：

1. **修改现有代码**
   - 添加新的统计指标（如平均订单金额）
   - 添加新的数据源（如从文件读取）
   - 添加新的输出（如发送到 Kafka）

2. **做自己的项目**
   - 实时统计网站访问量
   - 实时监测设备温度
   - 实时分析用户行为

3. **深入学习**
   - 学习 Flink SQL
   - 学习 Flink CEP（复杂事件处理）
   - 学习 Flink Table API

---

**🎉 恭喜你完成了 Flink 入门教程！现在你已经掌握了实时计算的基础知识！**

有任何问题，欢迎在 GitHub 上提 Issue 或加入 Flink 社区交流！

*教程版本：1.0.0 | 最后更新：2026-03-11*
