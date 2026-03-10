# Apache Flink 基础教程

> 作者：JOJO  
> 最后更新：2026 年 3 月 10 日  
> 版本：1.0

---

## 目录

1. [Flink 简介](#1-flink-简介)
2. [环境搭建](#2-环境搭建)
3. [核心概念](#3-核心概念)
4. [DataStream API](#4-datastream-api)
5. [Table API & SQL](#5-table-api--sql)
6. [窗口操作](#6-窗口操作)
7. [状态管理](#7-状态管理)
8. [容错机制](#8-容错机制)
9. [连接器](#9-连接器)
10. [实战示例](#10-实战示例)

---

## 1. Flink 简介

### 什么是 Flink？

Apache Flink 是一个分布式流处理引擎，专为**无界和有界数据流**设计。

### Flink 的特点

- ✅ **真正的流处理**：原生支持流处理，批处理是批量的流
- ✅ **低延迟**：毫秒级处理延迟
- ✅ **高吞吐**：每秒处理数百万事件
- ✅ **Exactly-Once 语义**：精确一次处理保证
- ✅ **事件时间处理**：支持事件时间语义
- ✅ **状态管理**：内置状态存储和管理

### Flink vs Spark Streaming

| 特性 | Flink | Spark Streaming |
|------|-------|-----------------|
| 处理模型 | 原生流处理 | 微批处理 |
| 延迟 | 毫秒级 | 秒级 |
| 事件时间 | 原生支持 | 有限支持 |
| 状态管理 | 内置 | 需要外部存储 |
| 反压处理 | 自动 | 有限 |

---

## 2. 环境搭建

### 安装 Flink

```bash
# 下载 Flink
wget https://archive.apache.org/dist/flink/flink-1.18.1/flink-1.18.1-bin-scala_2.12.tgz

# 解压
tar -xzf flink-1.18.1-bin-scala_2.12.tgz
cd flink-1.18.1

# 启动 Flink（本地模式）
./bin/start-cluster.sh

# 访问 Web UI
# http://localhost:8081
```

### Maven 依赖

```xml
<dependencies>
    <!-- Flink 核心 -->
    <dependency>
        <groupId>org.apache.flink</groupId>
        <artifactId>flink-streaming-java</artifactId>
        <version>1.18.1</version>
    </dependency>
    
    <!-- Flink 客户端 -->
    <dependency>
        <groupId>org.apache.flink</groupId>
        <artifactId>flink-clients</artifactId>
        <version>1.18.1</version>
    </dependency>
    
    <!-- Kafka 连接器 -->
    <dependency>
        <groupId>org.apache.flink</groupId>
        <artifactId>flink-connector-kafka</artifactId>
        <version>1.18.1</version>
    </dependency>
    
    <!-- JSON 处理 -->
    <dependency>
        <groupId>com.alibaba</groupId>
        <artifactId>fastjson</artifactId>
        <version>2.0.40</version>
    </dependency>
</dependencies>
```

---

## 3. 核心概念

### 3.1 流（Stream）

- **无界流**：有开始无结束的数据流（如 Kafka 消息）
- **有界流**：有开始有结束的数据流（如文件）

### 3.2 算子（Operator）

```java
// Source - 数据源
DataStream<String> stream = env.socketTextStream("localhost", 9999);

// Transformation - 转换
DataStream<String> transformed = stream
    .flatMap(new FlatMapFunction<String, String>() {
        @Override
        public void flatMap(String value, Collector<String> out) {
            for (String word : value.split(" ")) {
                out.collect(word);
            }
        }
    });

// Sink - 输出
transformed.print();
```

### 3.3 并行度

```java
// 设置并行度
env.setParallelism(4);

// 单个算子设置并行度
stream.map(...).setParallelism(2);

// 使用本地执行
env.execute("Job Name");
```

### 3.4 时间语义

- **事件时间（Event Time）**：事件实际发生的时间
- **处理时间（Processing Time）**：事件被处理的时间
- **摄入时间（Ingestion Time）**：事件进入 Flink 的时间

```java
// 分配时间戳和水位线
stream.assignTimestampsAndWatermarks(
    WatermarkStrategy
        .<Event>forBoundedOutOfOrderness(Duration.ofSeconds(5))
        .withTimestampAssigner((event, timestamp) -> event.getTimestamp())
);
```

---

## 4. DataStream API

### 4.1 创建 DataStream

```java
StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();

// 从集合
DataStream<String> fromCollection = env.fromCollection(Arrays.asList("a", "b", "c"));

// 从文件
DataStream<String> fromFile = env.readTextFile("path/to/file");

// 从 Socket
DataStream<String> fromSocket = env.socketTextStream("localhost", 9999);

// 自定义 Source
DataStream<String> custom = env.addSource(new SourceFunction<String>() {
    @Override
    public void run(SourceContext<String> ctx) throws Exception {
        while (true) {
            ctx.collect("data");
            Thread.sleep(1000);
        }
    }
    
    @Override
    public void cancel() {}
});
```

### 4.2 转换操作

```java
// Map - 一对一转换
stream.map(s -> s.toUpperCase());

// FlatMap - 一对多转换
stream.flatMap((String s, Collector<String> out) -> {
    for (String word : s.split(" ")) {
        out.collect(word);
    }
});

// Filter - 过滤
stream.filter(s -> s.length() > 3);

// KeyBy - 按键分组
stream.keyBy(value -> value.getField());

// Reduce - 归约
stream.keyBy(...).reduce((v1, v2) -> v1 + v2);

// Aggregation - 聚合
stream.keyBy(...).sum(1);
stream.keyBy(...).min(1);
stream.keyBy(...).max(1);
```

### 4.3 多流转换

```java
// Union - 合并多个流
stream1.union(stream2, stream3);

// Connect - 连接两个流
ConnectedStreams<String, Integer> connected = stream1.connect(stream2);

// CoMap - 共同映射
connected.map(
    new CoMapFunction<String, Integer, String>() {
        @Override
        public String map1(String value) { return "Stream1: " + value; }
        
        @Override
        public String map2(Integer value) { return "Stream2: " + value; }
    }
);

// Broadcast - 广播流
MapStateDescriptor<String, Integer> descriptor = 
    new MapStateDescriptor<>("broadcast", String.class, Integer.class);
BroadcastStream<String> broadcast = stream.broadcast(descriptor);
```

---

## 5. Table API & SQL

### 5.1 创建 TableEnvironment

```java
EnvironmentSettings settings = EnvironmentSettings.newInstance()
    .inStreamingMode()
    .build();

TableEnvironment tableEnv = TableEnvironment.create(settings);
```

### 5.2 创建表

```java
// DDL 创建表
tableEnv.executeSql("""
    CREATE TABLE orders (
        order_id BIGINT,
        user_id BIGINT,
        amount DECIMAL(10, 2),
        order_time TIMESTAMP(3),
        WATERMARK FOR order_time AS order_time - INTERVAL '5' SECOND
    ) WITH (
        'connector' = 'kafka',
        'topic' = 'orders',
        'properties.bootstrap.servers' = 'localhost:9092',
        'format' = 'json',
        'scan.startup.mode' = 'latest-offset'
    )
""");

// 从 DataStream 转换
DataStream<Order> orders = env.addSource(...);
tableEnv.createTemporaryView("orders", orders, "orderId, userId, amount, orderTime");
```

### 5.3 SQL 查询

```java
// 基本查询
Table result = tableEnv.sqlQuery("""
    SELECT user_id, SUM(amount) as total
    FROM orders
    WHERE amount > 100
    GROUP BY user_id
""");

// 窗口聚合
Table windowResult = tableEnv.sqlQuery("""
    SELECT 
        user_id,
        TUMBLE_START(order_time, INTERVAL '1' HOUR) as window_start,
        SUM(amount) as total
    FROM orders
    GROUP BY user_id, TUMBLE(order_time, INTERVAL '1' HOUR)
""");

// 写入 Sink
tableEnv.executeSql("""
    CREATE TABLE sink_table (
        user_id BIGINT,
        total DECIMAL(10, 2)
    ) WITH (
        'connector' = 'print'
    )
""");

result.executeInsert("sink_table");
```

### 5.4 Table API

```java
import static org.apache.flink.table.api.Expressions.*;

Table result = orders
    .filter($("amount").isGreater(100))
    .groupBy($("user_id"))
    .select($("user_id"), $("amount").sum().as("total"));
```

---

## 6. 窗口操作

### 6.1 滚动窗口（Tumbling Window）

```java
// 时间窗口
stream
    .keyBy(value -> value.getKey())
    .window(TumblingEventTimeWindows.of(Time.minutes(5)))
    .reduce(new ReduceFunction<Event>() {
        @Override
        public Event reduce(Event v1, Event v2) {
            return new Event(v1.getKey(), v1.getValue() + v2.getValue());
        }
    });

// 计数窗口
stream
    .keyBy(value -> value.getKey())
    .countWindow(100)
    .sum("value");
```

### 6.2 滑动窗口（Sliding Window）

```java
stream
    .keyBy(value -> value.getKey())
    .window(SlidingEventTimeWindows.of(
        Time.minutes(10),  // 窗口大小
        Time.minutes(5)    // 滑动步长
    ))
    .reduce(...);
```

### 6.3 会话窗口（Session Window）

```java
stream
    .keyBy(value -> value.getKey())
    .window(EventTimeSessionWindows.withGap(Time.minutes(5)))
    .reduce(...);
```

### 6.4 窗口函数

```java
// ReduceFunction
stream.keyBy(...).window(...).reduce(
    (v1, v2) -> new Event(v1.getKey(), v1.getValue() + v2.getValue())
);

// AggregateFunction
stream.keyBy(...).window(...).aggregate(
    new AggregateFunction<Long, Tuple2<Long, Long>, Long>() {
        @Override
        public Tuple2<Long, Long> createAccumulator() {
            return new Tuple2<>(0L, 0L);
        }
        
        @Override
        public Tuple2<Long, Long> add(Event value, Tuple2<Long, Long> acc) {
            acc.f0 += value.getValue();
            acc.f1++;
            return acc;
        }
        
        @Override
        public Long getResult(Tuple2<Long, Long> acc) {
            return acc.f0 / acc.f1;  // 返回平均值
        }
        
        @Override
        public Tuple2<Long, Long> merge(Tuple2<Long, Long> a, Tuple2<Long, Long> b) {
            return new Tuple2<>(a.f0 + b.f0, a.f1 + b.f1);
        }
    }
);

// ProcessWindowFunction（获取窗口元数据）
stream.keyBy(...).window(...).process(
    new ProcessWindowFunction<Event, String, String, TimeWindow>() {
        @Override
        public void process(String key, Context ctx, Iterable<Event> elements, Collector<String> out) {
            long count = 0;
            for (Event e : elements) count++;
            
            out.collect(String.format("Key: %s, Window: [%d, %d), Count: %d",
                key, ctx.window().getStart(), ctx.window().getEnd(), count));
        }
    }
);
```

---

## 7. 状态管理

### 7.1 ValueState

```java
public class CountWithThresholdFunction 
    extends RichFlatMapFunction<Event, Tuple2<String, Long>> {
    
    private transient ValueState<Long> countState;
    private final long threshold;
    
    public CountWithThresholdFunction(long threshold) {
        this.threshold = threshold;
    }
    
    @Override
    public void open(Configuration parameters) {
        ValueStateDescriptor<Long> descriptor = new ValueStateDescriptor<>(
            "count", Types.LONG
        );
        countState = getRuntimeContext().getState(descriptor);
    }
    
    @Override
    public void flatMap(Event event, Collector<Tuple2<String, Long>> out) {
        Long currentCount = countState.value();
        currentCount = (currentCount == null) ? 0 : currentCount;
        
        currentCount++;
        countState.update(currentCount);
        
        if (currentCount >= threshold) {
            out.collect(new Tuple2<>(event.getKey(), currentCount));
            countState.clear();
        }
    }
}
```

### 7.2 ListState

```java
public class BufferFunction extends RichFlatMapFunction<Event, List<Event>> {
    
    private transient ListState<Event> bufferState;
    private final int bufferSize;
    
    @Override
    public void open(Configuration parameters) {
        ListStateDescriptor<Event> descriptor = new ListStateDescriptor<>(
            "buffer", Event.class
        );
        bufferState = getRuntimeContext().getListState(descriptor);
    }
    
    @Override
    public void flatMap(Event event, Collector<List<Event>> out) {
        bufferState.add(event);
        
        List<Event> buffer = new ArrayList<>();
        for (Event e : bufferState.get()) {
            buffer.add(e);
        }
        
        if (buffer.size() >= bufferSize) {
            out.collect(buffer);
            bufferState.clear();
        }
    }
}
```

### 7.3 MapState

```java
public class UserSessionFunction 
    extends RichFlatMapFunction<Event, Tuple2<String, Session>> {
    
    private transient MapState<String, Session> sessionState;
    
    @Override
    public void open(Configuration parameters) {
        MapStateDescriptor<String, Session> descriptor = new MapStateDescriptor<>(
            "sessions", String.class, Session.class
        );
        sessionState = getRuntimeContext().getMapState(descriptor);
    }
    
    @Override
    public void flatMap(Event event, Collector<Tuple2<String, Session>> out) {
        String userId = event.getUserId();
        Session session = sessionState.get(userId);
        
        if (session == null) {
            session = new Session();
        }
        
        session.addEvent(event);
        sessionState.put(userId, session);
        
        if (session.isComplete()) {
            out.collect(new Tuple2<>(userId, session));
            sessionState.remove(userId);
        }
    }
}
```

### 7.4 状态后端

```java
// 内存状态后端（默认）
env.setStateBackend(new HashMapStateBackend());

// RocksDB 状态后端（适合大状态）
env.setStateBackend(new RocksDBStateBackend("hdfs://namenode:8020/flink/checkpoints"));

// 设置检查点
env.enableCheckpointing(60000);  // 60 秒
env.getCheckpointConfig().setMinPauseBetweenCheckpoints(30000);
```

---

## 8. 容错机制

### 8.1 Checkpoint 配置

```java
// 启用检查点
env.enableCheckpointing(60000);  // 60 秒间隔

CheckpointConfig config = env.getCheckpointConfig();

// 最小暂停时间
config.setMinPauseBetweenCheckpoints(30000);

// 超时时间
config.setCheckpointTimeout(60000);

// 最大并发检查点
config.setMaxConcurrentCheckpoints(1);

// 外部化检查点
config.enableExternalizedCheckpoints(
    CheckpointConfig.ExternalizedCheckpointCleanup.RETAIN_ON_CANCELLATION
);

// 语义保证
env.getCheckpointConfig().setCheckpointingMode(CheckpointingMode.EXACTLY_ONCE);
```

### 8.2 Savepoint

```bash
# 创建 Savepoint
./bin/flink savepoint <jobId> [targetDirectory]

# 从 Savepoint 恢复
./bin/flink run -s <savepointPath> <jarFile>

# 取消作业并创建 Savepoint
./bin/flink cancel -s <targetDirectory> <jobId>
```

### 8.3 重启策略

```java
// 固定延迟重启
env.setRestartStrategy(RestartStrategies.fixedDelayRestart(
    3,  // 重试次数
    Time.of(10, TimeUnit.SECONDS)  // 间隔
));

// 失败率重启
env.setRestartStrategy(RestartStrategies.failureRateRestart(
    3,  // 最大失败次数
    Time.of(5, TimeUnit.MINUTES),  // 时间窗口
    Time.of(10, TimeUnit.SECONDS)  // 间隔
));

// 不重启
env.setRestartStrategy(RestartStrategies.noRestart());
```

---

## 9. 连接器

### 9.1 Kafka Source

```java
// Flink 1.14+ 新 Kafka Source
KafkaSource<String> kafkaSource = KafkaSource.<String>builder()
    .setBootstrapServers("localhost:9092")
    .setTopics("input-topic")
    .setGroupId("flink-group")
    .setStartingOffsets(OffsetsInitializer.latest())
    .setValueOnlyDeserializer(new SimpleStringSchema())
    .build();

DataStream<String> stream = env.fromSource(
    kafkaSource, 
    WatermarkStrategy.noWatermarks(), 
    "Kafka Source"
);
```

### 9.2 Kafka Sink

```java
// Flink 1.14+ 新 Kafka Sink
KafkaSink<String> kafkaSink = KafkaSink.<String>builder()
    .setBootstrapServers("localhost:9092")
    .setRecordSerializer(new KafkaRecordSerializer<>(
        "output-topic",
        new SimpleStringSchema()
    ))
    .setDeliveryGuarantee(DeliveryGuarantee.EXACTLY_ONCE)
    .setTransactionalIdPrefix("flink-tx-")
    .build();

stream.sinkTo(kafkaSink);
```

### 9.3 JDBC Connector

```java
// 写入 JDBC
JdbcSink.sink(
    "INSERT INTO users (id, name, age) VALUES (?, ?, ?)",
    (statement, user) -> {
        statement.setLong(1, user.id);
        statement.setString(2, user.name);
        statement.setInt(3, user.age);
    },
    JdbcExecutionOptions.builder().withBatchSize(1000).build(),
    JdbcConnectionOptions.JdbcConnectionOptionsBuilder()
        .withUrl("jdbc:mysql://localhost:3306/test")
        .withDriverName("com.mysql.cj.jdbc.Driver")
        .withUsername("root")
        .withPassword("password")
        .build()
);
```

### 9.4 Elasticsearch Connector

```java
ElasticsearchSink.Builder<Event> esSinkBuilder = new ElasticsearchSink.Builder<>(
    Arrays.asList(new HttpHost("localhost", 9200, "http")),
    new ElasticsearchSinkFunction<Event>() {
        @Override
        public void process(Event element, RuntimeContext ctx, RequestIndexer indexer) {
            indexer.add(Requests.indexRequest()
                .index("events")
                .source(JSON.toJSONString(element), XContentType.JSON));
        }
    }
);

stream.addSink(esSinkBuilder.build());
```

### 9.5 HBase Connector

```java
// HBase Sink
stream.addSink(new HBaseSink<>(
    HBaseConfiguration.create(),
    "table_name",
    new HBaseMapper<Event>() {
        @Override
        public byte[] key(Event event) {
            return event.getId().getBytes();
        }
        
        @Override
        public void mutate(Event event, Mutation mutation) throws Exception {
            mutation.addColumn("cf".getBytes(), "name".getBytes(), event.getName().getBytes());
        }
    }
));
```

---

## 10. 实战示例

### 10.1 实时词频统计

```java
public class WordCountStream {
    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.setParallelism(1);
        
        DataStream<String> text = env.socketTextStream("localhost", 9999);
        
        DataStream<Tuple2<String, Long>> result = text
            .flatMap((String line, Collector<Tuple2<String, Long>> out) -> {
                for (String word : line.split("\\s+")) {
                    out.collect(new Tuple2<>(word, 1L));
                }
            })
            .keyBy(value -> value.f0)
            .sum(1);
        
        result.print();
        
        env.execute("Socket Window WordCount");
    }
}
```

### 10.2 实时订单统计

```java
public class OrderStatistics {
    
    public static class Order {
        public String userId;
        public double amount;
        public long timestamp;
        
        // getters, setters, constructors
    }
    
    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        env.enableCheckpointing(60000);
        
        // Kafka Source
        KafkaSource<String> source = KafkaSource.<String>builder()
            .setBootstrapServers("localhost:9092")
            .setTopics("orders")
            .setGroupId("flink-order-group")
            .setValueOnlyDeserializer(new SimpleStringSchema())
            .build();
        
        DataStream<Order> orders = env.fromSource(source, WatermarkStrategy.noWatermarks(), "Orders")
            .map(json -> JSON.parseObject(json, Order.class));
        
        // 每小时销售额统计
        orders
            .assignTimestampsAndWatermarks(
                WatermarkStrategy.<Order>forBoundedOutOfOrderness(Duration.ofSeconds(5))
                    .withTimestampAssigner((order, ts) -> order.timestamp)
            )
            .keyBy(order -> order.userId)
            .window(TumblingEventTimeWindows.of(Time.hours(1)))
            .aggregate(new AggregateFunction<Order, Double, Double>() {
                @Override
                public Double createAccumulator() { return 0.0; }
                
                @Override
                public Double add(Order order, Double acc) { return acc + order.amount; }
                
                @Override
                public Double getResult(Double acc) { return acc; }
                
                @Override
                public Double merge(Double a, Double b) { return a + b; }
            })
            .addSink(JdbcSink.sink(
                "INSERT INTO hourly_sales (user_id, total, window_end) VALUES (?, ?, ?)",
                (stmt, result) -> {
                    stmt.setString(1, result.f0);
                    stmt.setDouble(2, result.f1);
                    stmt.setTimestamp(3, new Timestamp(result.f2));
                },
                ...
            ));
        
        env.execute("Order Statistics");
    }
}
```

### 10.3 实时风控系统

```java
public class FraudDetection {
    
    public static class Transaction {
        public String userId;
        public double amount;
        public long timestamp;
        public String type;
    }
    
    public static class Alert {
        public String userId;
        public String reason;
        public long timestamp;
    }
    
    public static void main(String[] args) throws Exception {
        StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
        
        KafkaSource<Transaction> source = ...;
        
        DataStream<Transaction> transactions = env.fromSource(source, ...);
        
        // 规则 1：单笔交易超过阈值
        DataStream<Alert> largeTransactionAlerts = transactions
            .filter(t -> t.amount > 10000)
            .map(t -> {
                Alert alert = new Alert();
                alert.userId = t.userId;
                alert.reason = "Large transaction: " + t.amount;
                alert.timestamp = t.timestamp;
                return alert;
            });
        
        // 规则 2：短时间内多次交易
        DataStream<Alert> frequentTransactionAlerts = transactions
            .keyBy(t -> t.userId)
            .window(SlidingEventTimeWindows.of(Time.minutes(5), Time.minutes(1)))
            .process(new ProcessWindowFunction<Transaction, Alert, String, TimeWindow>() {
                @Override
                public void process(String userId, Context ctx, Iterable<Transaction> elements, Collector<Alert> out) {
                    int count = 0;
                    for (Transaction t : elements) count++;
                    
                    if (count > 10) {
                        Alert alert = new Alert();
                        alert.userId = userId;
                        alert.reason = "Frequent transactions: " + count + " in 5 minutes";
                        alert.timestamp = ctx.window().getEnd();
                        out.collect(alert);
                    }
                }
            });
        
        // 合并告警
        largeTransactionAlerts
            .union(frequentTransactionAlerts)
            .addSink(KafkaSink.<Alert>builder()
                .setBootstrapServers("localhost:9092")
                .setRecordSerializer(...)
                .build());
        
        env.execute("Fraud Detection");
    }
}
```

---

## 附录：常用命令

```bash
# 提交作业
./bin/flink run -c com.example.MyJob my-job.jar

# 指定并行度
./bin/flink run -p 4 -c com.example.MyJob my-job.jar

# 从 Savepoint 恢复
./bin/flink run -s hdfs:///flink/savepoints/savepoint-xxx -c com.example.MyJob my-job.jar

# 列出作业
./bin/flink list

# 取消作业
./bin/flink cancel <jobId>

# 停止作业（带 Savepoint）
./bin/flink stop -d <jobId>

# 查看作业日志
./bin/flink log <jobId>

# 触发 Checkpoint
./bin/flink savepoint <jobId>
```

---

## 学习资源

- **官方文档**: https://nightlies.apache.org/flink/flink-docs-stable/
- **GitHub**: https://github.com/apache/flink
- **Flink 实战**: https://github.com/apache/flink-playgrounds
- **Flink 学习**: https://flink-learning.org.cn/

---

**祝你 Flink 学习愉快！** 🚀

*作者：JOJO*
