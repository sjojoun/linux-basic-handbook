# Apache HBase 基础教程

> 作者：JOJO  
> 最后更新：2026 年 3 月 10 日  
> 版本：1.0

---

## 目录

1. [HBase 简介](#1-hbase-简介)
2. [核心概念](#2-核心概念)
3. [环境搭建](#3-环境搭建)
4. [Shell 操作](#4-shell-操作)
5. [Java API](#5-java-api)
6. [数据模型](#6-数据模型)
7. [表设计](#7-表设计)
8. [高级特性](#8-高级特性)
9. [性能优化](#9-性能优化)
10. [实战示例](#10-实战示例)

---

## 1. HBase 简介

### 什么是 HBase？

Apache HBase 是一个分布式、可扩展的**列式存储数据库**，运行在 HDFS 之上，提供随机、实时的读写访问。

### HBase 的特点

- ✅ **海量数据**：支持十亿行、百万列
- ✅ **强一致性**：行级强一致性读写
- ✅ **自动分区**：表自动分片（Region）
- ✅ **高可用**：支持主从复制和故障转移
- ✅ **Schema-free**：列族预定义，列动态添加

### HBase vs RDBMS

| 特性 | HBase | RDBMS |
|------|-------|-------|
| 数据模型 | 列式存储 | 行式存储 |
| Schema | 动态 | 静态 |
| 扩展性 | 水平扩展 | 垂直扩展 |
| 事务 | 行级事务 | 多行事务 |
| 查询 | 主键查询 | SQL 复杂查询 |
| 适用场景 | 大数据、实时查询 | OLTP、复杂事务 |

### 使用场景

- 实时数据查询（用户画像、订单查询）
- 时序数据（监控数据、日志存储）
- 消息存储（聊天记录、推送历史）
- 倒排索引（搜索索引）

---

## 2. 核心概念

### 2.1 数据模型

```
Table
├── RowKey (行键)
│   ├── Column Family: cf1
│   │   ├── Column: cf1:name
│   │   ├── Column: cf1:age
│   ├── Column Family: cf2
│   │   ├── Column: cf2:address
│   │   ├── Column: cf2:phone
```

### 2.2 核心组件

- **Table**：表，由多个 Region 组成
- **Region**：表的分区，按 RowKey 范围划分
- **RowKey**：行键，唯一标识一行
- **Column Family**：列族，物理存储单位
- **Column Qualifier**：列限定符，动态添加
- **Cell**：单元格，存储数据的基本单位
- **Timestamp**：时间戳，版本标识

### 2.3 架构组件

- **HMaster**：主节点，管理元数据和 Region 分配
- **RegionServer**：区域服务器，处理数据读写
- **ZooKeeper**：协调服务，维护集群状态
- **HDFS**：底层存储

---

## 3. 环境搭建

### 3.1 单机模式

```bash
# 下载 HBase
wget https://archive.apache.org/dist/hbase/2.5.6/hbase-2.5.6-bin.tar.gz

# 解压
tar -xzf hbase-2.5.6-bin.tar.gz
cd hbase-2.5.6

# 配置
cat > conf/hbase-site.xml << EOF
<configuration>
  <property>
    <name>hbase.rootdir</name>
    <value>file:///tmp/hbase-${user.name}</value>
  </property>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>false</value>
  </property>
</configuration>
EOF

# 启动
./bin/start-hbase.sh

# 访问 Web UI
# http://localhost:16010
```

### 3.2 伪分布式

```xml
<!-- conf/hbase-site.xml -->
<configuration>
  <property>
    <name>hbase.rootdir</name>
    <value>hdfs://localhost:8020/hbase</value>
  </property>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.dataDir</name>
    <value>/tmp/zookeeper</value>
  </property>
</configuration>
```

### 3.3 完全分布式

```xml
<!-- conf/hbase-site.xml -->
<configuration>
  <property>
    <name>hbase.rootdir</name>
    <value>hdfs://namenode:8020/hbase</value>
  </property>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>zk1,zk2,zk3</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
  </property>
  <property>
    <name>hbase.master</name>
    <value>hdfs://namenode:8020/hbase/.hbase-version</value>
  </property>
</configuration>
```

### 3.4 Maven 依赖

```xml
<dependencies>
    <dependency>
        <groupId>org.apache.hbase</groupId>
        <artifactId>hbase-client</artifactId>
        <version>2.5.6</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hbase</groupId>
        <artifactId>hbase-server</artifactId>
        <version>2.5.6</version>
    </dependency>
</dependencies>
```

---

## 4. Shell 操作

### 4.1 进入 Shell

```bash
./bin/hbase shell
```

### 4.2 表操作

```ruby
# 创建表
create 'users', 'info', 'address'
create 'orders', {NAME => 'detail', VERSIONS => 3}
create 'logs', {NAME => 'cf', COMPRESSION => 'SNAPPY'}

# 查看表
list
describe 'users'
exists 'users'

# 禁用表
disable 'users'

# 启用表
enable 'users'

# 删除表
disable 'users'
drop 'users'

# 截断表
truncate 'users'
```

### 4.3 CRUD 操作

```ruby
# 插入数据
put 'users', 'user001', 'info:name', 'Alice'
put 'users', 'user001', 'info:age', '25'
put 'users', 'user001', 'address:city', 'Beijing'

# 批量插入
put 'users', 'user002', {
  'info:name' => 'Bob',
  'info:age' => '30',
  'address:city' => 'Shanghai'
}

# 查询数据
get 'users', 'user001'
get 'users', 'user001', 'info:name'
get 'users', 'user001', {COLUMN => 'info:name'}
get 'users', 'user001', {VERSIONS => 3}

# 扫描表
scan 'users'
scan 'users', {COLUMNS => ['info:name', 'info:age']}
scan 'users', {STARTROW => 'user001', STOPROW => 'user010'}
scan 'users', {FILTER => "ValueFilter(=, 'binary:Alice')"}

# 更新数据
put 'users', 'user001', 'info:age', '26'

# 删除数据
delete 'users', 'user001', 'info:age'
deleteall 'users', 'user001'
```

### 4.4 高级查询

```ruby
# 过滤器查询
scan 'users', {
  FILTER => "SingleColumnValueFilter('info', 'age', =, 'binary:25')"
}

# 行前缀过滤器
scan 'users', {
  FILTER => "PrefixFilter('binary:user00')"
}

# 分页过滤器
scan 'users', {
  FILTER => "PageFilter(100)",
  STARTROW => 'user001'
}

# 组合过滤器
scan 'users', {
  FILTER => "SingleColumnValueFilter('info', 'age', >=, 'binary:20') AND " +
            "SingleColumnValueFilter('info', 'age', <=, 'binary:30')"
}

# 正则过滤器
scan 'users', {
  FILTER => "ValueFilter(=, 'substring:Bei')"
}
```

### 4.5 表管理

```ruby
# 修改表结构
alter 'users', 'new_cf'
alter 'users', {NAME => 'info', VERSIONS => 5}
alter 'users', {NAME => 'info', COMPRESSION => 'GZ'}
alter 'users', METHOD => :delete, NAME => 'new_cf'

# 查看 Region 分布
status 'users'

# 手动 split
split 'users'
split 'users', 'user050'

# 手动 compact
compact 'users'
major_compact 'users'

# 查看表大小
du 'users'
```

---

## 5. Java API

### 5.1 连接配置

```java
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.*;

public class HBaseExample {
    
    private static Connection connection;
    
    public static void init() throws Exception {
        Configuration config = HBaseConfiguration.create();
        config.set("hbase.zookeeper.quorum", "localhost");
        config.set("hbase.zookeeper.property.clientPort", "2181");
        config.set("hbase.client.retries.number", "3");
        
        connection = ConnectionFactory.createConnection(config);
    }
    
    public static void close() throws Exception {
        if (connection != null && !connection.isClosed()) {
            connection.close();
        }
    }
}
```

### 5.2 创建表

```java
public static void createTable(String tableName, String... columnFamilies) 
    throws Exception {
    
    Admin admin = connection.getAdmin();
    
    if (admin.tableExists(TableName.valueOf(tableName))) {
        System.out.println("Table already exists!");
        return;
    }
    
    TableDescriptorBuilder tableBuilder = TableDescriptorBuilder.newBuilder(
        TableName.valueOf(tableName)
    );
    
    for (String cf : columnFamilies) {
        ColumnFamilyDescriptor cfBuilder = ColumnFamilyDescriptorBuilder
            .newBuilder(Bytes.toBytes(cf))
            .setMaxVersions(3)
            .setCompressionType(Compression.Algorithm.SNAPPY)
            .build();
        tableBuilder.setColumnFamily(cfBuilder);
    }
    
    admin.createTable(tableBuilder.build());
    admin.close();
}

// 使用
createTable("users", "info", "address");
```

### 5.3 插入数据

```java
public static void putData(String tableName, String rowKey, 
    String columnFamily, String column, String value) throws Exception {
    
    Table table = connection.getTable(TableName.valueOf(tableName));
    
    Put put = new Put(Bytes.toBytes(rowKey));
    put.addColumn(
        Bytes.toBytes(columnFamily),
        Bytes.toBytes(column),
        Bytes.toBytes(value)
    );
    
    table.put(put);
    table.close();
}

// 批量插入
public static void batchPut(String tableName, List<Put> puts) throws Exception {
    Table table = connection.getTable(TableName.valueOf(tableName));
    table.put(puts);
    table.close();
}

// 使用
Put put1 = new Put(Bytes.toBytes("user001"));
put1.addColumn(Bytes.toBytes("info"), Bytes.toBytes("name"), Bytes.toBytes("Alice"));
put1.addColumn(Bytes.toBytes("info"), Bytes.toBytes("age"), Bytes.toBytes("25"));

Put put2 = new Put(Bytes.toBytes("user002"));
put2.addColumn(Bytes.toBytes("info"), Bytes.toBytes("name"), Bytes.toBytes("Bob"));
put2.addColumn(Bytes.toBytes("info"), Bytes.toBytes("age"), Bytes.toBytes("30"));

batchPut("users", Arrays.asList(put1, put2));
```

### 5.4 查询数据

```java
// 单行查询
public static Result getRow(String tableName, String rowKey) throws Exception {
    Table table = connection.getTable(TableName.valueOf(tableName));
    Get get = new Get(Bytes.toBytes(rowKey));
    get.setMaxVersions(3);
    Result result = table.get(get);
    table.close();
    return result;
}

// 多行查询
public static List<Result> getRows(String tableName, List<String> rowKeys) 
    throws Exception {
    
    Table table = connection.getTable(TableName.valueOf(tableName));
    List<Get> gets = new ArrayList<>();
    
    for (String rowKey : rowKeys) {
        gets.add(new Get(Bytes.toBytes(rowKey)));
    }
    
    Result[] results = table.get(gets);
    table.close();
    return Arrays.asList(results);
}

// 扫描查询
public static void scanTable(String tableName) throws Exception {
    Table table = connection.getTable(TableName.valueOf(tableName));
    Scan scan = new Scan();
    scan.setCaching(100);
    scan.setBatch(10);
    
    try (ResultScanner scanner = table.getScanner(scan)) {
        for (Result result : scanner) {
            System.out.println("RowKey: " + Bytes.toString(result.getRow()));
            for (Cell cell : result.rawCells()) {
                System.out.println(
                    "CF: " + Bytes.toString(CellUtil.cloneFamily(cell)) +
                    ", Column: " + Bytes.toString(CellUtil.cloneQualifier(cell)) +
                    ", Value: " + Bytes.toString(CellUtil.cloneValue(cell))
                );
            }
        }
    }
    table.close();
}
```

### 5.5 过滤器

```java
// 单列值过滤器
SingleColumnValueFilter filter1 = new SingleColumnValueFilter(
    Bytes.toBytes("info"),
    Bytes.toBytes("age"),
    CompareOperator.GREATER,
    Bytes.toBytes("20")
);

// 行前缀过滤器
PrefixFilter filter2 = new PrefixFilter(Bytes.toBytes("user00"));

// 分页过滤器
PageFilter filter3 = new PageFilter(100);

// 组合过滤器
FilterList filterList = new FilterList(FilterList.Operator.MUST_PASS_ALL);
filterList.addFilter(filter1);
filterList.addFilter(filter2);

// 使用过滤器
Scan scan = new Scan();
scan.setFilter(filterList);

Table table = connection.getTable(TableName.valueOf("users"));
ResultScanner scanner = table.getScanner(scan);
```

### 5.6 删除数据

```java
// 删除列
public static void deleteColumn(String tableName, String rowKey, 
    String columnFamily, String column) throws Exception {
    
    Table table = connection.getTable(TableName.valueOf(tableName));
    Delete delete = new Delete(Bytes.toBytes(rowKey));
    delete.addColumn(Bytes.toBytes(columnFamily), Bytes.toBytes(column));
    table.delete(delete);
    table.close();
}

// 删除行
public static void deleteRow(String tableName, String rowKey) throws Exception {
    Table table = connection.getTable(TableName.valueOf(tableName));
    Delete delete = new Delete(Bytes.toBytes(rowKey));
    table.delete(delete);
    table.close();
}

// 批量删除
public static void batchDelete(String tableName, List<String> rowKeys) 
    throws Exception {
    
    Table table = connection.getTable(TableName.valueOf(tableName));
    List<Delete> deletes = new ArrayList<>();
    
    for (String rowKey : rowKeys) {
        deletes.add(new Delete(Bytes.toBytes(rowKey)));
    }
    
    table.delete(deletes);
    table.close();
}
```

### 5.7 原子操作

```java
// 检查并放入（Check And Put）
public static boolean checkAndPut(String tableName, String rowKey,
    String columnFamily, String column, String checkValue, String newValue) 
    throws Exception {
    
    Table table = connection.getTable(TableName.valueOf(tableName));
    
    boolean success = table.checkAndPut(
        Bytes.toBytes(rowKey),
        Bytes.toBytes(columnFamily),
        Bytes.toBytes(column),
        Bytes.toBytes(checkValue),
        new Put(Bytes.toBytes(rowKey))
            .addColumn(Bytes.toBytes(columnFamily), Bytes.toBytes(column), 
                      Bytes.toBytes(newValue))
    );
    
    table.close();
    return success;
}

// 递增操作
public static long increment(String tableName, String rowKey,
    String columnFamily, String column, long amount) throws Exception {
    
    Table table = connection.getTable(TableName.valueOf(tableName));
    long newValue = table.incrementColumnValue(
        Bytes.toBytes(rowKey),
        Bytes.toBytes(columnFamily),
        Bytes.toBytes(column),
        amount
    );
    table.close();
    return newValue;
}
```

---

## 6. 数据模型

### 6.1 RowKey 设计

```java
// 好的 RowKey 设计原则
// 1. 唯一性
// 2. 长度适中（16-100 字节）
// 3. 散列分布（避免热点）
// 4. 有序性（支持范围查询）

// 示例 1：时间戳 + 用户 ID（避免热点）
String rowKey1 = String.format("%s_%s", 
    String.format("%020d", System.currentTimeMillis()), 
    userId
);

// 示例 2：哈希前缀 + 业务 ID
String rowKey2 = String.format("%s_%s", 
    MD5(userId).substring(0, 8), 
    userId
);

// 示例 3：反转字符串（散列）
String rowKey3 = new StringBuilder(phoneNumber).reverse().toString();

// 示例 4：组合键
String rowKey4 = String.format("%s_%s_%s", 
    regionCode, 
    String.format("%010d", userId), 
    String.format("%020d", timestamp)
);
```

### 6.2 列族设计

```java
// 列族设计原则
// 1. 数量不宜过多（1-3 个）
// 2. 长度适中（1-10 字符）
// 3. 访问模式相似的列放在一起

// 示例：用户表
ColumnFamilyDescriptor info = ColumnFamilyDescriptorBuilder
    .newBuilder(Bytes.toBytes("info"))
    .setMaxVersions(3)           // 最大版本数
    .setMinVersions(1)           // 最小版本数
    .setTimeToLive(2592000)      // TTL 30 天
    .setCompressionType(Compression.Algorithm.SNAPPY)  // 压缩
    .setInMemory(false)          // 是否内存存储
    .setBlocksize(65536)         // 块大小
    .setBloomFilterType(BloomType.ROW)  // 布隆过滤器
    .build();

ColumnFamilyDescriptor behavior = ColumnFamilyDescriptorBuilder
    .newBuilder(Bytes.toBytes("behavior"))
    .setMaxVersions(1)
    .setTimeToLive(604800)       // TTL 7 天
    .setCompressionType(Compression.Algorithm.SNAPPY)
    .build();
```

### 6.3 Cell 结构

```java
// Cell 包含的信息
// 1. RowKey
// 2. Column Family
// 3. Column Qualifier
// 4. Timestamp
// 5. Value

// 读取 Cell 信息
Result result = table.get(new Get(Bytes.toBytes("user001")));

for (Cell cell : result.rawCells()) {
    String rowKey = Bytes.toString(CellUtil.cloneRow(cell));
    String family = Bytes.toString(CellUtil.cloneFamily(cell));
    String qualifier = Bytes.toString(CellUtil.cloneQualifier(cell));
    long timestamp = cell.getTimestamp();
    String value = Bytes.toString(CellUtil.cloneValue(cell));
    
    System.out.printf("RowKey: %s, CF: %s, Column: %s, TS: %d, Value: %s%n",
        rowKey, family, qualifier, timestamp, value);
}
```

---

## 7. 表设计

### 7.1 用户画像表

```java
public class UserProfileTable {
    
    public static void create(Connection connection) throws Exception {
        Admin admin = connection.getAdmin();
        
        TableDescriptor descriptor = TableDescriptorBuilder
            .newBuilder(TableName.valueOf("user_profile"))
            .setColumnFamily(ColumnFamilyDescriptorBuilder
                .newBuilder(Bytes.toBytes("base"))
                .setMaxVersions(1)
                .setCompressionType(Compression.Algorithm.SNAPPY)
                .build())
            .setColumnFamily(ColumnFamilyDescriptorBuilder
                .newBuilder(Bytes.toBytes("tags"))
                .setMaxVersions(1)
                .setTimeToLive(2592000)
                .setCompressionType(Compression.Algorithm.SNAPPY)
                .build())
            .setColumnFamily(ColumnFamilyDescriptorBuilder
                .newBuilder(Bytes.toBytes("stats"))
                .setMaxVersions(30)
                .setTimeToLive(7776000)  // 90 天
                .setCompressionType(Compression.Algorithm.SNAPPY)
                .build())
            .build();
        
        if (!admin.tableExists(descriptor.getTableName())) {
            admin.createTable(descriptor);
        }
        
        admin.close();
    }
    
    // RowKey 设计：用户 ID
    // base: 基本信息（姓名、性别、年龄）
    // tags: 用户标签（动态更新）
    // stats: 统计信息（最近 90 天）
}
```

### 7.2 订单表

```java
public class OrderTable {
    
    public static void create(Connection connection) throws Exception {
        Admin admin = connection.getAdmin();
        
        TableDescriptor descriptor = TableDescriptorBuilder
            .newBuilder(TableName.valueOf("orders"))
            .setColumnFamily(ColumnFamilyDescriptorBuilder
                .newBuilder(Bytes.toBytes("detail"))
                .setMaxVersions(1)
                .setCompressionType(Compression.Algorithm.SNAPPY)
                .build())
            .setColumnFamily(ColumnFamilyDescriptorBuilder
                .newBuilder(Bytes.toBytes("status"))
                .setMaxVersions(100)
                .setTimeToLive(7776000)
                .setCompressionType(Compression.Algorithm.SNAPPY)
                .build())
            .build();
        
        admin.createTable(descriptor);
        admin.close();
    }
    
    // RowKey 设计：订单 ID 反转（避免热点）
    // 或者：用户 ID_ 时间戳_ 订单 ID（支持按用户查询）
    public static String buildRowKey(String userId, long timestamp, String orderId) {
        return String.format("%s_%020d_%s", userId, timestamp, orderId);
    }
    
    // detail: 订单详情（商品、金额、地址）
    // status: 订单状态流转（创建、支付、发货、完成）
}
```

### 7.3 时序数据表

```java
public class TimeSeriesTable {
    
    public static void create(Connection connection) throws Exception {
        Admin admin = connection.getAdmin();
        
        TableDescriptor descriptor = TableDescriptorBuilder
            .newBuilder(TableName.valueOf("metrics"))
            .setColumnFamily(ColumnFamilyDescriptorBuilder
                .newBuilder(Bytes.toBytes("m"))  // 单列族，简短命名
                .setMaxVersions(1)
                .setTimeToLive(2592000)  // 30 天
                .setCompressionType(Compression.Algorithm.SNAPPY)
                .setDataBlockEncoding(DataBlockEncoding.FAST_DIFF)
                .build())
            .build();
        
        admin.createTable(descriptor);
        admin.close();
    }
    
    // RowKey 设计：指标名_ 时间戳_ 标签
    // 例如：cpu.usage_1647302400000_host=server1,dc=beijing
    public static String buildRowKey(String metric, long timestamp, 
        Map<String, String> tags) {
        
        StringBuilder sb = new StringBuilder();
        sb.append(metric).append("_");
        sb.append(String.format("%020d", timestamp)).append("_");
        
        List<String> tagList = new ArrayList<>();
        for (Map.Entry<String, String> entry : tags.entrySet()) {
            tagList.add(entry.getKey() + "=" + entry.getValue());
        }
        Collections.sort(tagList);  // 标签排序，保证一致性
        
        sb.append(String.join(",", tagList));
        return sb.toString();
    }
}
```

---

## 8. 高级特性

### 8.1 协处理器

```java
// 观察器协处理器示例
public class MyObserver extends BaseRegionObserver {
    
    @Override
    public void prePut(ObserverContext<RegionCoprocessorEnvironment> e, 
        Put put, WALEdit edit, Durability durability) throws IOException {
        
        // 数据验证
        Cell cell = put.getCell(Bytes.toBytes("info"), Bytes.toBytes("age"));
        if (cell != null) {
            int age = Bytes.toInt(CellUtil.cloneValue(cell));
            if (age < 0 || age > 150) {
                throw new IOException("Invalid age: " + age);
            }
        }
    }
    
    @Override
    public void preGetOp(ObserverContext<RegionCoprocessorEnvironment> e,
        Get get, List<Cell> result) throws IOException {
        
        // 权限检查
        String user = getCurrentUser();
        if (!hasPermission(user, get)) {
            throw new IOException("Access denied");
        }
    }
}

// 注册协处理器
ColumnFamilyDescriptor cf = ColumnFamilyDescriptorBuilder
    .newBuilder(Bytes.toBytes("info"))
    .setCoprocessor(CoprocessorDescriptorBuilder
        .newBuilder("com.example.MyObserver")
        .setJarPath("hdfs://namenode:8020/lib/my-coprocessor.jar")
        .setPriority(100)
        .build())
    .build();
```

### 8.2 过滤器链

```java
// 组合过滤器
FilterList filterList = new FilterList(FilterList.Operator.MUST_PASS_ALL);

// 条件 1：年龄 >= 20
SingleColumnValueFilter ageFilter = new SingleColumnValueFilter(
    Bytes.toBytes("info"),
    Bytes.toBytes("age"),
    CompareOperator.GREATER_OR_EQUAL,
    Bytes.toBytes("20")
);

// 条件 2：城市 = Beijing
SingleColumnValueFilter cityFilter = new SingleColumnValueFilter(
    Bytes.toBytes("address"),
    Bytes.toBytes("city"),
    CompareOperator.EQUAL,
    Bytes.toBytes("Beijing")
);

// 条件 3：RowKey 前缀
PrefixFilter prefixFilter = new PrefixFilter(Bytes.toBytes("user00"));

filterList.addFilter(ageFilter);
filterList.addFilter(cityFilter);
filterList.addFilter(prefixFilter);

Scan scan = new Scan();
scan.setFilter(filterList);
```

### 8.3 布隆过滤器

```java
// 布隆过滤器类型
// NONE: 不使用
// ROW: 行级布隆过滤
// ROWCOL: 行 + 列级布隆过滤

ColumnFamilyDescriptor cf = ColumnFamilyDescriptorBuilder
    .newBuilder(Bytes.toBytes("info"))
    .setBloomFilterType(BloomType.ROW)  // 推荐
    // .setBloomFilterType(BloomType.ROWCOL)  // 更精确，占用更多空间
    .build();

// 布隆过滤器可以加速 Get 操作，减少磁盘 IO
```

### 8.4 块缓存

```java
// 配置块缓存
Configuration config = HBaseConfiguration.create();

// BlockCache 实现选择
config.set("hfile.block.cache.size", "0.4");  // 40% 堆内存用于块缓存
config.set("hbase.bucketcache.size", "2048");  // BucketCache 大小（MB）
config.set("hbase.bucketcache.ioengine", "offheap");  // 堆外缓存

// 列族级别的缓存配置
ColumnFamilyDescriptor cf = ColumnFamilyDescriptorBuilder
    .newBuilder(Bytes.toBytes("info"))
    .setInMemory(true)      // 优先保留在内存
    .setBlocksize(65536)    // 块大小
    .build();
```

---

## 9. 性能优化

### 9.1 Write 优化

```java
// 1. 批量写入
List<Put> puts = new ArrayList<>(1000);
for (int i = 0; i < 1000; i++) {
    Put put = new Put(Bytes.toBytes("user" + String.format("%06d", i)));
    put.addColumn(Bytes.toBytes("info"), Bytes.toBytes("name"), 
                 Bytes.toBytes("User" + i));
    puts.add(put);
}
table.put(puts);  // 批量提交

// 2. 关闭自动刷新
table.setWriteBufferSize(1024 * 1024 * 10);  // 10MB 缓冲
table.setWriteBufferPeriodicFlush(5000);     // 5 秒刷新

// 3. 禁用 WAL（仅用于可丢失数据）
Put put = new Put(Bytes.toBytes("rowkey"));
put.setDurability(Durability.SKIP_WAL);
table.put(put);

// 4. 预创建 Region
byte[][] splits = new byte[9][];
for (int i = 1; i < 10; i++) {
    splits[i-1] = Bytes.toBytes(String.format("user%06d", i * 100000));
}
admin.createTable(descriptor, splits);
```

### 9.2 Read 优化

```java
// 1. 设置缓存
Scan scan = new Scan();
scan.setCaching(100);      // 每次 RPC 获取 100 行
scan.setBatch(10);         // 每行最多 10 列

// 2. 指定列族/列
scan.addColumn(Bytes.toBytes("info"), Bytes.toBytes("name"));
scan.addFamily(Bytes.toBytes("info"));

// 3. 使用布隆过滤器
// 在表设计时启用

// 4. 并行扫描
List<Scan> scans = new ArrayList<>();
// ... 创建多个 Scan
ResultScanner[] scanners = new ResultScanner[scans.size()];
// 并行执行扫描
```

### 9.3 压缩优化

```java
// 压缩算法比较
// NONE: 无压缩
// SNAPPY: 快速，推荐（需要 native 库）
// GZ: 高压缩比，较慢
// LZ4: 快速，HBase 默认

ColumnFamilyDescriptor cf = ColumnFamilyDescriptorBuilder
    .newBuilder(Bytes.toBytes("info"))
    .setCompressionType(Compression.Algorithm.SNAPPY)
    .build();

// 启用数据块编码
ColumnFamilyDescriptor cf2 = ColumnFamilyDescriptorBuilder
    .newBuilder(Bytes.toBytes("data"))
    .setDataBlockEncoding(DataBlockEncoding.FAST_DIFF)
    .build();
```

### 9.4 预分区

```java
// 预分区策略
// 1. 等分预分区
byte[][] splits1 = new byte[15][];
for (int i = 1; i < 16; i++) {
    splits1[i-1] = Bytes.toBytes(String.format("%02d", i * 6));
}

// 2. 基于哈希预分区
byte[][] splits2 = new byte[15][];
for (int i = 1; i < 16; i++) {
    splits2[i-1] = Bytes.toBytes(String.format("%02x", i * 16));
}

// 3. 基于业务预分区
byte[][] splits3 = new byte[][]{
    Bytes.toBytes("beijing"),
    Bytes.toBytes("shanghai"),
    Bytes.toBytes("guangzhou"),
    Bytes.toBytes("shenzhen")
};

admin.createTable(descriptor, splits);
```

---

## 10. 实战示例

### 10.1 用户画像系统

```java
public class UserProfileSystem {
    
    private Connection connection;
    private Table profileTable;
    
    public void init() throws Exception {
        Configuration config = HBaseConfiguration.create();
        config.set("hbase.zookeeper.quorum", "zk1,zk2,zk3");
        connection = ConnectionFactory.createConnection(config);
        profileTable = connection.getTable(TableName.valueOf("user_profile"));
    }
    
    // 更新用户基本信息
    public void updateBaseInfo(String userId, Map<String, String> info) 
        throws Exception {
        
        Put put = new Put(Bytes.toBytes(userId));
        for (Map.Entry<String, String> entry : info.entrySet()) {
            put.addColumn(Bytes.toBytes("base"), Bytes.toBytes(entry.getKey()), 
                         Bytes.toBytes(entry.getValue()));
        }
        profileTable.put(put);
    }
    
    // 添加用户标签
    public void addTags(String userId, List<String> tags) throws Exception {
        Put put = new Put(Bytes.toBytes(userId));
        long timestamp = System.currentTimeMillis();
        
        for (String tag : tags) {
            put.addColumn(Bytes.toBytes("tags"), Bytes.toBytes(tag), 
                         timestamp, Bytes.toBytes("1"));
        }
        profileTable.put(put);
    }
    
    // 获取用户画像
    public UserProfile getProfile(String userId) throws Exception {
        Get get = new Get(Bytes.toBytes(userId));
        Result result = profileTable.get(get);
        
        UserProfile profile = new UserProfile();
        profile.setUserId(userId);
        
        // 读取基本信息
        NavigableMap<byte[], byte[]> base = 
            result.getFamilyMap(Bytes.toBytes("base"));
        for (Map.Entry<byte[], byte[]> entry : base.entrySet()) {
            String key = Bytes.toString(entry.getKey());
            String value = Bytes.toString(entry.getValue());
            profile.setBaseInfo(key, value);
        }
        
        // 读取最新标签
        NavigableMap<byte[], byte[]> tags = 
            result.getFamilyMap(Bytes.toBytes("tags"));
        for (byte[] qualifier : tags.keySet()) {
            profile.addTag(Bytes.toString(qualifier));
        }
        
        return profile;
    }
    
    // 查询特定标签的用户
    public List<String> getUsersByTag(String tag) throws Exception {
        List<String> users = new ArrayList<>();
        
        Filter filter = new SingleColumnValueFilter(
            Bytes.toBytes("tags"),
            Bytes.toBytes(tag),
            CompareOperator.EQUAL,
            Bytes.toBytes("1")
        );
        
        Scan scan = new Scan();
        scan.setFilter(filter);
        
        try (ResultScanner scanner = profileTable.getScanner(scan)) {
            for (Result result : scanner) {
                users.add(Bytes.toString(result.getRow()));
            }
        }
        
        return users;
    }
}
```

### 10.2 订单查询系统

```java
public class OrderQuerySystem {
    
    private Connection connection;
    private Table orderTable;
    
    // RowKey: userId_timestamp_orderId
    public void createOrder(Order order) throws Exception {
        String rowKey = String.format("%s_%020d_%s", 
            order.getUserId(), 
            order.getTimestamp(), 
            order.getOrderId()
        );
        
        Put put = new Put(Bytes.toBytes(rowKey));
        
        // 订单详情
        put.addColumn(Bytes.toBytes("detail"), Bytes.toBytes("amount"), 
                     Bytes.toBytes(String.valueOf(order.getAmount())));
        put.addColumn(Bytes.toBytes("detail"), Bytes.toBytes("items"), 
                     Bytes.toBytes(order.getItemsJson()));
        put.addColumn(Bytes.toBytes("detail"), Bytes.toBytes("address"), 
                     Bytes.toBytes(order.getAddress()));
        
        // 订单状态
        put.addColumn(Bytes.toBytes("status"), 
                     Bytes.toBytes(String.valueOf(order.getStatus().getCode())), 
                     Bytes.toBytes(String.valueOf(System.currentTimeMillis())));
        
        orderTable.put(put);
    }
    
    // 查询用户订单
    public List<Order> getUserOrders(String userId, long startTime, long endTime) 
        throws Exception {
        
        List<Order> orders = new ArrayList<>();
        
        String startRow = String.format("%s_%020d_", userId, startTime);
        String stopRow = String.format("%s_%020d_", userId, endTime);
        
        Scan scan = new Scan();
        scan.withStartRow(Bytes.toBytes(startRow));
        scan.withStopRow(Bytes.toBytes(stopRow));
        scan.addColumn(Bytes.toBytes("detail"), Bytes.toBytes("amount"));
        
        try (ResultScanner scanner = orderTable.getScanner(scan)) {
            for (Result result : scanner) {
                Order order = parseOrder(result);
                orders.add(order);
            }
        }
        
        return orders;
    }
    
    // 查询订单状态历史
    public List<OrderStatus> getOrderStatusHistory(String orderId) 
        throws Exception {
        
        // 需要先通过二级索引找到 RowKey
        // 这里简化处理
        List<OrderStatus> statuses = new ArrayList<>();
        
        Get get = new Get(Bytes.toBytes(orderId));
        get.addFamily(Bytes.toBytes("status"));
        
        Result result = orderTable.get(get);
        NavigableMap<byte[], byte[]> statusMap = 
            result.getFamilyMap(Bytes.toBytes("status"));
        
        for (Map.Entry<byte[], byte[]> entry : statusMap.entrySet()) {
            int statusCode = Bytes.toInt(entry.getKey());
            long timestamp = Bytes.toLong(entry.getValue());
            statuses.add(new OrderStatus(statusCode, timestamp));
        }
        
        return statuses;
    }
}
```

### 10.3 监控数据存储

```java
public class MetricsStorage {
    
    private Connection connection;
    private Table metricsTable;
    
    // 存储监控指标
    public void saveMetric(String metric, Map<String, String> tags, 
        long timestamp, double value) throws Exception {
        
        String rowKey = buildRowKey(metric, timestamp, tags);
        
        Put put = new Put(Bytes.toBytes(rowKey));
        put.addColumn(Bytes.toBytes("m"), Bytes.toBytes("v"), 
                     Bytes.toBytes(String.valueOf(value)));
        
        metricsTable.put(put);
    }
    
    // 批量存储
    public void saveMetrics(List<Metric> metrics) throws Exception {
        List<Put> puts = new ArrayList<>(metrics.size());
        
        for (Metric metric : metrics) {
            String rowKey = buildRowKey(metric.getName(), 
                metric.getTimestamp(), metric.getTags());
            
            Put put = new Put(Bytes.toBytes(rowKey));
            put.addColumn(Bytes.toBytes("m"), Bytes.toBytes("v"), 
                         Bytes.toBytes(String.valueOf(metric.getValue())));
            puts.add(put);
        }
        
        metricsTable.put(puts);
    }
    
    // 查询指标
    public List<MetricData> queryMetrics(String metric, long startTime, 
        long endTime, Map<String, String> tags) throws Exception {
        
        List<MetricData> results = new ArrayList<>();
        
        String startRow = buildRowKey(metric, startTime, tags);
        String stopRow = buildRowKey(metric, endTime + 1, tags);
        
        Scan scan = new Scan();
        scan.withStartRow(Bytes.toBytes(startRow));
        scan.withStopRow(Bytes.toBytes(stopRow));
        scan.addColumn(Bytes.toBytes("m"), Bytes.toBytes("v"));
        
        try (ResultScanner scanner = metricsTable.getScanner(scan)) {
            for (Result result : scanner) {
                MetricData data = parseMetricData(result);
                results.add(data);
            }
        }
        
        return results;
    }
    
    private String buildRowKey(String metric, long timestamp, 
        Map<String, String> tags) {
        // 实现 RowKey 构建逻辑
        return metric + "_" + String.format("%020d", timestamp);
    }
}
```

---

## 附录：常用命令

```bash
# 启动 HBase
./bin/start-hbase.sh

# 停止 HBase
./bin/stop-hbase.sh

# 进入 Shell
./bin/hbase shell

# 查看集群状态
./bin/hbase hbck

# 查看 RegionServer 状态
./bin/hbase regionserver-status

# 平衡 Region
./bin/hbase shell
> balance_switch true
> balance

# 启用/禁用表
> disable 'table_name'
> enable 'table_name'

# 查看日志
tail -f logs/hbase-hmaster-hostname.log
tail -f logs/hbase-regionserver-hostname.log
```

---

## 学习资源

- **官方文档**: https://hbase.apache.org/book.html
- **GitHub**: https://github.com/apache/hbase
- **HBase 权威指南**: https://hbasebook.com/

---

**祝你 HBase 学习愉快！** 🚀

*作者：JOJO*
