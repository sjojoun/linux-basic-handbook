# Apache Flume 基础教程

> 作者：JOJO  
> 最后更新：2026 年 3 月 10 日  
> 版本：1.0

---

## 目录

1. [Flume 简介](#1-flume-简介)
2. [核心概念](#2-核心概念)
3. [环境搭建](#3-环境搭建)
4. [Source 配置](#4-source-配置)
5. [Channel 配置](#5-channel-配置)
6. [Sink 配置](#6-sink-配置)
7. [拦截器](#7-拦截器)
8. [选择器](#8-选择器)
9. [高级配置](#9-高级配置)
10. [实战示例](#10-实战示例)

---

## 1. Flume 简介

### 什么是 Flume？

Apache Flume 是一个分布式、可靠、高可用的**日志收集系统**，用于高效收集、聚合和移动大量日志数据。

### Flume 的特点

- ✅ **分布式架构**：支持水平扩展
- ✅ **高可靠性**：事务机制保证数据不丢失
- ✅ **高可用性**：支持故障转移
- ✅ **可扩展性**：自定义 Source、Channel、Sink
- ✅ **流式处理**：实时数据收集

### Flume 使用场景

- 日志收集（Web 服务器日志、应用日志）
- 数据采集（传感器数据、点击流）
- 数据聚合（多源数据汇聚）
- 实时数据管道（Kafka、HDFS 等）

---

## 2. 核心概念

### 2.1 Agent

Flume 的基本运行单元，包含三个核心组件：

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│    Source   │───▶│   Channel   │───▶│    Sink     │
│  (数据源)    │    │   (通道)     │    │  (输出)     │
└─────────────┘    └─────────────┘    └─────────────┘
```

### 2.2 Source

负责接收数据，支持多种数据源：

- **Exec Source**：执行命令（如 tail -F）
- **Netcat Source**：监听端口
- **Spooling Directory Source**：监控目录
- **Kafka Source**：从 Kafka 消费
- **HTTP Source**：HTTP 接口

### 2.3 Channel

Source 和 Sink 之间的缓冲区：

- **Memory Channel**：内存存储（快速，可能丢失）
- **File Channel**：文件存储（可靠，较慢）
- **Kafka Channel**：Kafka 作为通道

### 2.4 Sink

负责输出数据，支持多种输出：

- **Logger Sink**：输出到日志
- **HDFS Sink**：写入 HDFS
- **Kafka Sink**：写入 Kafka
- **HBase Sink**：写入 HBase
- **Elasticsearch Sink**：写入 ES

### 2.5 Event

Flume 数据传输的基本单元：

```
┌─────────────────────────────────────┐
│              Event                  │
├─────────────────────────────────────┤
│  Headers: {key1=value1, key2=value2}│
│  Body: [binary data]                │
└─────────────────────────────────────┘
```

---

## 3. 环境搭建

### 3.1 安装 Flume

```bash
# 下载 Flume
wget https://archive.apache.org/dist/flume/1.11.0/apache-flume-1.11.0-bin.tar.gz

# 解压
tar -xzf apache-flume-1.11.0-bin.tar.gz
cd apache-flume-1.11.0

# 配置环境变量
export FLUME_HOME=/path/to/flume
export PATH=$PATH:$FLUME_HOME/bin

# 验证安装
flume-ng version
```

### 3.2 目录结构

```
flume/
├── bin/           # 可执行脚本
├── conf/          # 配置文件
│   ├── flume-env.sh
│   └── flume-conf.properties
├── lib/           # 依赖库
└── logs/          # 日志目录
```

### 3.3 配置文件

```properties
# flume-env.sh
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk
export FLUME_HEAPSIZE=1024
```

---

## 4. Source 配置

### 4.1 Exec Source（执行命令）

```properties
a1.sources = r1
a1.channels = c1

a1.sources.r1.type = exec
a1.sources.r1.command = tail -F /var/log/app.log
a1.sources.r1.channels = c1
```

### 4.2 Netcat Source（监听端口）

```properties
a1.sources = r1
a1.channels = c1

a1.sources.r1.type = netcat
a1.sources.r1.bind = localhost
a1.sources.r1.port = 44444
a1.sources.r1.channels = c1
```

### 4.3 Spooling Directory Source（监控目录）

```properties
a1.sources = r1
a1.channels = c1

a1.sources.r1.type = spooldir
a1.sources.r1.spoolDir = /var/log/flume/incoming
a1.sources.r1.fileHeader = true
a1.sources.r1.fileHeaderKey = filename
a1.sources.r1.channels = c1
```

### 4.4 Kafka Source

```properties
a1.sources = r1
a1.channels = c1

a1.sources.r1.type = org.apache.flume.source.kafka.KafkaSource
a1.sources.r1.channels = c1
a1.sources.r1.batchSize = 5000
a1.sources.r1.batchDurationMillis = 2000
a1.sources.r1.kafka.bootstrap.servers = localhost:9092
a1.sources.r1.kafka.topics = test-topic
a1.sources.r1.kafka.consumer.group.id = flume-consumer
```

### 4.5 HTTP Source

```properties
a1.sources = r1
a1.channels = c1

a1.sources.r1.type = http
a1.sources.r1.port = 5140
a1.sources.r1.channels = c1
a1.sources.r1.handler = org.apache.flume.source.http.JSONHandler
```

### 4.6 Taildir Source（推荐）

```properties
a1.sources = r1
a1.channels = c1

a1.sources.r1.type = TAILDIR
a1.sources.r1.positionFile = /var/log/flume/taildir_position.json
a1.sources.r1.filegroups = f1 f2

# 文件组 1
a1.sources.r1.filegroups.f1 = /var/log/app/.*\\.log
a1.sources.r1.filegroups.f2 = /var/log/system/.*\\.log

a1.sources.r1.interceptors = i1
a1.sources.r1.interceptors.i1.type = host
a1.sources.r1.interceptors.i1.hostHeader = hostname

a1.sources.r1.channels = c1
```

---

## 5. Channel 配置

### 5.1 Memory Channel（内存通道）

```properties
a1.channels = c1

a1.channels.c1.type = memory
a1.channels.c1.capacity = 10000
a1.channels.c1.transactionCapacity = 1000
a1.channels.c1.byteCapacityBufferPercentage = 20
a1.channels.c1.byteCapacity = 800000
```

### 5.2 File Channel（文件通道）

```properties
a1.channels = c1

a1.channels.c1.type = file
a1.channels.c1.checkpointDir = /var/log/flume/checkpoint
a1.channels.c1.dataDirs = /var/log/flume/data
a1.channels.c1.maxFileSize = 2146435071
a1.channels.c1.capacity = 1000000
a1.channels.c1.keep-alive = 30
```

### 5.3 Kafka Channel

```properties
a1.channels = c1

a1.channels.c1.type = org.apache.flume.channel.kafka.KafkaChannel
a1.channels.c1.capacity = 10000
a1.channels.c1.transactionCapacity = 1000
a1.channels.c1.kafka.bootstrap.servers = localhost:9092
a1.channels.c1.kafka.topic = flume-channel
a1.channels.c1.kafka.consumer.group.id = flume-channel-group
```

### 5.4 JDBC Channel

```properties
a1.channels = c1

a1.channels.c1.type = jdbc
a1.channels.c1.driver.url = jdbc:derby:;databaseName=/var/log/flume/db;create=true
a1.channels.c1.driver.user = flume
a1.channels.c1.driver.password = flume
a1.channels.c1.db.table = FLUME_EVENT
```

---

## 6. Sink 配置

### 6.1 Logger Sink（日志输出）

```properties
a1.sinks = k1
a1.channels = c1

a1.sinks.k1.type = logger
a1.sinks.k1.channel = c1
```

### 6.2 HDFS Sink

```properties
a1.sinks = k1
a1.channels = c1

a1.sinks.k1.type = hdfs
a1.sinks.k1.channel = c1
a1.sinks.k1.hdfs.path = hdfs://namenode:8020/flume/%Y/%m/%d/%H
a1.sinks.k1.hdfs.filePrefix = events
a1.sinks.k1.hdfs.fileSuffix = .log
a1.sinks.k1.hdfs.fileType = DataStream
a1.sinks.k1.hdfs.rollInterval = 300
a1.sinks.k1.hdfs.rollSize = 134217728
a1.sinks.k1.hdfs.rollCount = 1000000
a1.sinks.k1.hdfs.batchSize = 100
a1.sinks.k1.hdfs.idleTimeout = 60
a1.sinks.k1.hdfs.timeZone = Asia/Shanghai
a1.sinks.k1.hdfs.useLocalTimeStamp = true
```

### 6.3 Kafka Sink

```properties
a1.sinks = k1
a1.channels = c1

a1.sinks.k1.type = org.apache.flume.sink.kafka.KafkaSink
a1.sinks.k1.channel = c1
a1.sinks.k1.kafka.bootstrap.servers = localhost:9092
a1.sinks.k1.kafka.topic = flume-output
a1.sinks.k1.kafka.flumeBatchSize = 20
a1.sinks.k1.kafka.producer.acks = 1
a1.sinks.k1.kafka.producer.linger.ms = 1
a1.sinks.k1.kafka.producer.compression.type = snappy
```

### 6.4 HBase Sink

```properties
a1.sinks = k1
a1.channels = c1

a1.sinks.k1.type = hbase
a1.sinks.k1.channel = c1
a1.sinks.k1.table = events
a1.sinks.k1.columnFamily = cf
a1.sinks.k1.serializer = org.apache.flume.sink.hbase.RegexHbaseEventSerializer
a1.sinks.k1.serializer.column = cf:data
a1.sinks.k1.serializer.pattern = ^([\\d]+)\\s+(.*)$
a1.sinks.k1.batchSize = 100
```

### 6.5 Elasticsearch Sink

```properties
a1.sinks = k1
a1.channels = c1

a1.sinks.k1.type = org.apache.flume.sink.elasticsearch.ElasticSearchSink
a1.sinks.k1.channel = c1
a1.sinks.k1.hostNames = localhost:9300
a1.sinks.k1.indexName = flume-%{+YYYY.MM.dd}
a1.sinks.k1.indexType = _doc
a1.sinks.k1.clusterName = elasticsearch
a1.sinks.k1.batchSize = 100
a1.sinks.k1.ttlDays = 30
```

### 6.6 File Roll Sink

```properties
a1.sinks = k1
a1.channels = c1

a1.sinks.k1.type = file_roll
a1.sinks.k1.channel = c1
a1.sinks.k1.sink.directory = /var/log/flume/output
a1.sinks.k1.sink.rollInterval = 30
```

---

## 7. 拦截器

### 7.1 Timestamp Interceptor（时间戳）

```properties
a1.sources.r1.interceptors = i1
a1.sources.r1.interceptors.i1.type = timestamp
```

### 7.2 Host Interceptor（主机名）

```properties
a1.sources.r1.interceptors = i1
a1.sources.r1.interceptors.i1.type = host
a1.sources.r1.interceptors.i1.hostHeader = hostname
a1.sources.r1.interceptors.i1.useIp = false
```

### 7.3 Static Interceptor（静态字段）

```properties
a1.sources.r1.interceptors = i1
a1.sources.r1.interceptors.i1.type = static
a1.sources.r1.interceptors.i1.key = datacenter
a1.sources.r1.interceptors.i1.value = dc1
```

### 7.4 Regex Extractor Interceptor（正则提取）

```properties
a1.sources.r1.interceptors = i1
a1.sources.r1.interceptors.i1.type = regex_extractor
a1.sources.r1.interceptors.i1.regex = ^([\\d]+)\\s+(\\w+)\\s+(.*)$
a1.sources.r1.interceptors.i1.serializers = s1 s2 s3
a1.sources.r1.interceptors.i1.serializers.s1.name = timestamp
a1.sources.r1.interceptors.i1.serializers.s1.type = org.apache.flume.interceptor.RegexExtractorInterceptorPassThroughSerializer
a1.sources.r1.interceptors.i1.serializers.s2.name = level
a1.sources.r1.interceptors.i1.serializers.s2.type = org.apache.flume.interceptor.RegexExtractorInterceptorPassThroughSerializer
a1.sources.r1.interceptors.i1.serializers.s3.name = message
a1.sources.r1.interceptors.i1.serializers.s3.type = org.apache.flume.interceptor.RegexExtractorInterceptorPassThroughSerializer
```

### 7.5 Search & Replace Interceptor（搜索替换）

```properties
a1.sources.r1.interceptors = i1
a1.sources.r1.interceptors.i1.type = search_replace
a1.sources.r1.interceptors.i1.searchRegex = password=\\w+
a1.sources.r1.interceptors.i1.replaceString = password=***
```

### 7.6 UUID Interceptor

```properties
a1.sources.r1.interceptors = i1
a1.sources.r1.interceptors.i1.type = uuid
a1.sources.r1.interceptors.i1.headerKey = uuid
```

---

## 8. 选择器

### 8.1 Default Channel Selector（默认）

```properties
# 默认复制到所有 Channel
a1.sources.r1.channels = c1 c2 c3
a1.sources.r1.selector.type = default
```

### 8.2 Replicating Channel Selector（复制）

```properties
a1.sources.r1.channels = c1 c2
a1.sources.r1.selector.type = replicating
# 可选：指定某些 Channel 为可选
a1.sources.r1.selector.optional = c2
```

### 8.3 Multiplexing Channel Selector（多路复用）

```properties
a1.sources.r1.channels = c1 c2 c3
a1.sources.r1.selector.type = multiplexing
a1.sources.r1.selector.header = level
a1.sources.r1.selector.mapping.ERROR = c1
a1.sources.r1.selector.mapping.WARN = c2
a1.sources.r1.selector.mapping.INFO = c3
a1.sources.r1.selector.default = c3
```

---

## 9. 高级配置

### 9.1 多 Agent 配置

```properties
# Agent 1 - 收集端
a1.sources = r1
a1.channels = c1
a1.sinks = k1

a1.sources.r1.type = exec
a1.sources.r1.command = tail -F /var/log/app.log
a1.sources.r1.channels = c1

a1.channels.c1.type = memory
a1.channels.c1.capacity = 10000

a1.sinks.k1.type = avro
a1.sinks.k1.channel = c1
a1.sinks.k1.hostname = 192.168.1.100
a1.sinks.k1.port = 41414

# Agent 2 - 聚合端
a2.sources = r2
a2.channels = c2
a2.sinks = k2

a2.sources.r2.type = avro
a2.sources.r2.channel = c2
a2.sources.r2.bind = 0.0.0.0
a2.sources.r2.port = 41414

a2.channels.c2.type = file
a2.channels.c2.checkpointDir = /var/log/flume/checkpoint

a2.sinks.k2.type = hdfs
a2.sinks.k2.channel = c2
a2.sinks.k2.hdfs.path = hdfs://namenode:8020/flume/%Y%m%d
```

### 9.2 负载均衡

```properties
# Sink Group 配置
a1.sinks = k1 k2 k3
a1.sinkgroups = g1

a1.sinkgroups.g1.sinks = k1 k2 k3
a1.sinkgroups.g1.processor.type = load_balance
a1.sinkgroups.g1.processor.backoff = true
a1.sinkgroups.g1.processor.selector = round_robin
```

### 9.3 故障转移

```properties
a1.sinks = k1 k2
a1.sinkgroups = g1

a1.sinkgroups.g1.sinks = k1 k2
a1.sinkgroups.g1.processor.type = failover
a1.sinkgroups.g1.processor.priority.k1 = 10
a1.sinkgroups.g1.processor.priority.k2 = 5
a1.sinkgroups.g1.processor.maxpenalty = 10000
```

### 9.4 批量处理

```properties
a1.sources.r1.batchSize = 1000
a1.sources.r1.batchTimeout = 1000

a1.channels.c1.capacity = 100000
a1.channels.c1.transactionCapacity = 10000

a1.sinks.k1.batchSize = 1000
a1.sinks.k1.batchDurationMillis = 5000
```

---

## 10. 实战示例

### 10.1 日志收集到 HDFS

```properties
# 配置名称
a1.sources = r1
a1.channels = c1
a1.sinks = k1

# Source - Taildir
a1.sources.r1.type = TAILDIR
a1.sources.r1.positionFile = /var/log/flume/taildir_position.json
a1.sources.r1.filegroups = app sys
a1.sources.r1.filegroups.app = /var/log/app/.*\\.log
a1.sources.r1.filegroups.sys = /var/log/syslog

# 拦截器
a1.sources.r1.interceptors = ts host
a1.sources.r1.interceptors.ts.type = timestamp
a1.sources.r1.interceptors.host.type = host
a1.sources.r1.interceptors.host.hostHeader = hostname

a1.sources.r1.channels = c1

# Channel - File
a1.channels.c1.type = file
a1.channels.c1.checkpointDir = /var/log/flume/checkpoint
a1.channels.c1.dataDirs = /var/log/flume/data
a1.channels.c1.capacity = 1000000

# Sink - HDFS
a1.sinks.k1.type = hdfs
a1.sinks.k1.channel = c1
a1.sinks.k1.hdfs.path = hdfs://namenode:8020/flume/logs/%Y/%m/%d/%H
a1.sinks.k1.hdfs.filePrefix = events
a1.sinks.k1.hdfs.fileSuffix = .log
a1.sinks.k1.hdfs.fileType = CompressStream
a1.sinks.k1.hdfs.codeC = gzip
a1.sinks.k1.hdfs.rollInterval = 300
a1.sinks.k1.hdfs.rollSize = 134217728
a1.sinks.k1.hdfs.rollCount = 1000000
a1.sinks.k1.hdfs.batchSize = 100
a1.sinks.k1.hdfs.timeZone = Asia/Shanghai
a1.sinks.k1.hdfs.useLocalTimeStamp = true
```

启动命令：
```bash
flume-ng agent \
  --conf /path/to/conf \
  --conf-file /path/to/flume-hdfs.conf \
  --name a1 \
  -Dflume.root.logger=INFO,console
```

### 10.2 Kafka 到 HDFS

```properties
a1.sources = r1
a1.channels = c1
a1.sinks = k1

# Source - Kafka
a1.sources.r1.type = org.apache.flume.source.kafka.KafkaSource
a1.sources.r1.channels = c1
a1.sources.r1.batchSize = 5000
a1.sources.r1.batchDurationMillis = 2000
a1.sources.r1.kafka.bootstrap.servers = kafka1:9092,kafka2:9092,kafka3:9092
a1.sources.r1.kafka.topics = app-logs
a1.sources.r1.kafka.consumer.group.id = flume-kafka-group
a1.sources.r1.kafka.consumer.security.protocol = SASL_SSL
a1.sources.r1.kafka.consumer.sasl.mechanism = PLAIN

# Channel - Memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 100000
a1.channels.c1.transactionCapacity = 10000

# Sink - HDFS
a1.sinks.k1.type = hdfs
a1.sinks.k1.channel = c1
a1.sinks.k1.hdfs.path = hdfs://namenode:8020/flume/kafka/%Y%m%d/%H
a1.sinks.k1.hdfs.filePrefix = logs
a1.sinks.k1.hdfs.fileSuffix = .parquet
a1.sinks.k1.hdfs.fileType = Parquet
a1.sinks.k1.hdfs.round = true
a1.sinks.k1.hdfs.roundValue = 10
a1.sinks.k1.hdfs.roundUnit = minute
a1.sinks.k1.hdfs.rollInterval = 0
a1.sinks.k1.hdfs.rollSize = 0
a1.sinks.k1.hdfs.rollCount = 0
a1.sinks.k1.hdfs.batchSize = 1000
```

### 10.3 多路复用日志路由

```properties
a1.sources = r1
a1.channels = c1 c2 c3
a1.sinks = k1 k2 k3

# Source
a1.sources.r1.type = exec
a1.sources.r1.command = tail -F /var/log/app.log
a1.sources.r1.channels = c1 c2 c3

# 选择器 - 按日志级别路由
a1.sources.r1.selector.type = multiplexing
a1.sources.r1.selector.header = level
a1.sources.r1.selector.mapping.ERROR = c1
a1.sources.r1.selector.mapping.WARN = c2
a1.sources.r1.selector.mapping.INFO = c3
a1.sources.r1.selector.default = c3

# 拦截器 - 提取日志级别
a1.sources.r1.interceptors = i1
a1.sources.r1.interceptors.i1.type = regex_extractor
a1.sources.r1.interceptors.i1.regex = ^(\\w+)\\s+.*$
a1.sources.r1.interceptors.i1.serializers = s1
a1.sources.r1.interceptors.i1.serializers.s1.name = level
a1.sources.r1.interceptors.i1.serializers.s1.type = org.apache.flume.interceptor.RegexExtractorInterceptorPassThroughSerializer

# Channel 1 - ERROR（高优先级，文件存储）
a1.channels.c1.type = file
a1.channels.c1.checkpointDir = /var/log/flume/error/checkpoint
a1.channels.c1.dataDirs = /var/log/flume/error/data

# Channel 2 - WARN
a1.channels.c2.type = memory
a1.channels.c2.capacity = 50000

# Channel 3 - INFO
a1.channels.c3.type = memory
a1.channels.c3.capacity = 100000

# Sink 1 - ERROR 到 HDFS（单独目录）
a1.sinks.k1.type = hdfs
a1.sinks.k1.channel = c1
a1.sinks.k1.hdfs.path = hdfs://namenode:8020/flume/error/%Y%m%d
a1.sinks.k1.hdfs.filePrefix = error
a1.sinks.k1.hdfs.rollInterval = 60

# Sink 2 - WARN 到 Kafka
a1.sinks.k2.type = org.apache.flume.sink.kafka.KafkaSink
a1.sinks.k2.channel = c2
a1.sinks.k2.kafka.bootstrap.servers = localhost:9092
a1.sinks.k2.kafka.topic = warn-logs

# Sink 3 - INFO 到 Elasticsearch
a1.sinks.k3.type = org.apache.flume.sink.elasticsearch.ElasticSearchSink
a1.sinks.k3.channel = c3
a1.sinks.k3.hostNames = localhost:9300
a1.sinks.k3.indexName = app-logs-%{+YYYY.MM.dd}
a1.sinks.k3.batchSize = 100
```

### 10.4 实时日志分析管道

```properties
# 完整的数据管道：App -> Flume -> Kafka -> Flume -> HDFS/ES

# Agent 1 - 收集端
agent1.sources = r1
agent1.channels = c1
agent1.sinks = k1

agent1.sources.r1.type = TAILDIR
agent1.sources.r1.positionFile = /var/log/flume/position.json
agent1.sources.r1.filegroups = app
agent1.sources.r1.filegroups.app = /var/log/app/.*\\.log

agent1.sources.r1.interceptors = ts host uuid
agent1.sources.r1.interceptors.ts.type = timestamp
agent1.sources.r1.interceptors.host.type = host
agent1.sources.r1.interceptors.uuid.type = uuid

agent1.channels.c1.type = memory
agent1.channels.c1.capacity = 100000

agent1.sinks.k1.type = org.apache.flume.sink.kafka.KafkaSink
agent1.sinks.k1.channel = c1
agent1.sinks.k1.kafka.bootstrap.servers = kafka-cluster:9092
agent1.sinks.k1.kafka.topic = raw-logs
agent1.sinks.k1.kafka.producer.acks = 1

# Agent 2 - 处理端
agent2.sources = r2
agent2.channels = c2 c3
agent2.sinks = k2 k3

# Kafka Source
agent2.sources.r2.type = org.apache.flume.source.kafka.KafkaSource
agent2.sources.r2.channels = c2 c3
agent2.sources.r2.kafka.bootstrap.servers = kafka-cluster:9092
agent2.sources.r2.kafka.topics = raw-logs
agent2.sources.r2.kafka.consumer.group.id = flume-processor

# 选择器 - 按业务类型路由
agent2.sources.r2.selector.type = multiplexing
agent2.sources.r2.header = biz_type
agent2.sources.r2.mapping.order = c2
agent2.sources.r2.mapping.user = c3

agent2.channels.c2.type = memory
agent2.channels.c2.capacity = 50000

agent2.channels.c3.type = file
agent2.channels.c3.checkpointDir = /var/log/flume/user/checkpoint

# HDFS Sink - 订单日志
agent2.sinks.k2.type = hdfs
agent2.sinks.k2.channel = c2
agent2.sinks.k2.hdfs.path = hdfs://namenode:8020/flume/orders/%Y%m%d/%H
agent2.sinks.k2.hdfs.filePrefix = orders
agent2.sinks.k2.hdfs.fileSuffix = .parquet
agent2.sinks.k2.hdfs.fileType = Parquet

# Elasticsearch Sink - 用户行为
agent2.sinks.k3.type = org.apache.flume.sink.elasticsearch.ElasticSearchSink
agent2.sinks.k3.channel = c3
agent2.sinks.k3.hostNames = es-cluster:9300
agent2.sinks.k3.indexName = user-behavior-%{+YYYY.MM.dd}
```

---

## 附录：常用命令

```bash
# 启动 Agent
flume-ng agent \
  --conf /path/to/conf \
  --conf-file /path/to/config.conf \
  --name agent-name \
  -Dflume.root.logger=INFO,console

# 启动 Master（已废弃，1.7+ 不再使用）
flume-ng master \
  --conf /path/to/conf \
  --conf-file /path/to/config.conf \
  --name master-name

# 查看帮助
flume-ng help

# 查看版本
flume-ng version

# 使用自定义 JVM 参数
flume-ng agent \
  --conf /path/to/conf \
  --conf-file /path/to/config.conf \
  --name agent-name \
  -Xmx2048m \
  -Xms1024m
```

---

## 学习资源

- **官方文档**: https://flume.apache.org/releases/content/1.11.0/FlumeUserGuide.html
- **GitHub**: https://github.com/apache/flume
- **Flume 架构**: https://flume.apache.org/FlumeDeveloperGuide.html

---

**祝你 Flume 学习愉快！** 🚀

*作者：JOJO*
