# Redis 基础教程

> 作者：JOJO  
> 最后更新：2026 年 3 月 10 日  
> 版本：1.0

---

## 目录

1. [Redis 简介](#1-redis-简介)
2. [环境搭建](#2-环境搭建)
3. [数据类型](#3-数据类型)
4. [字符串操作](#4-字符串操作)
5. [哈希操作](#5-哈希操作)
6. [列表操作](#6-列表操作)
7. [集合操作](#7-集合操作)
8. [有序集合](#8-有序集合)
9. [高级特性](#9-高级特性)
10. [实战示例](#10-实战示例)

---

## 1. Redis 简介

### 什么是 Redis？

Redis（Remote Dictionary Server）是一个开源的**内存数据结构存储系统**，可用作数据库、缓存和消息中间件。

### Redis 的特点

- ✅ **高性能**：读写速度 10 万 + QPS
- ✅ **丰富数据类型**：String、Hash、List、Set、ZSet
- ✅ **持久化**：RDB 快照 + AOF 日志
- ✅ **主从复制**：支持数据冗余
- ✅ **高可用**：Sentinel 哨兵模式
- ✅ **分布式**：Redis Cluster 集群

### 使用场景

- 缓存（热点数据、会话存储）
- 计数器（点赞、浏览量）
- 排行榜（游戏、电商）
- 消息队列（发布订阅、Stream）
- 分布式锁（秒杀、抢购）

---

## 2. 环境搭建

### 2.1 安装 Redis

```bash
# Linux 安装
wget https://download.redis.io/releases/redis-7.2.3.tar.gz
tar -xzf redis-7.2.3.tar.gz
cd redis-7.2.3
make
make install

# macOS 安装
brew install redis

# Windows 安装（使用 WSL 或下载 Windows 版本）
# https://github.com/microsoftarchive/redis/releases
```

### 2.2 启动 Redis

```bash
# 前台启动
redis-server

# 后台启动
redis-server --daemonize yes

# 指定配置文件
redis-server /etc/redis/redis.conf

# 检查是否运行
redis-cli ping  # 返回 PONG
```

### 2.3 配置文件

```conf
# redis.conf 关键配置

# 网络
bind 127.0.0.1
port 6379
protected-mode yes

# 通用
daemonize yes
pidfile /var/run/redis_6379.pid
loglevel notice
logfile /var/log/redis/redis.log

# 持久化
save 900 1
save 300 10
save 60 10000
dbfilename dump.rdb
dir /var/lib/redis

appendonly yes
appendfilename "appendonly.aof"
appendfsync everysec

# 内存管理
maxmemory 2gb
maxmemory-policy allkeys-lru

# 安全
requirepass your_password

# 主从复制
# replicaof <masterip> <masterport>
# masterauth <master-password>
```

### 2.3 Maven 依赖

```xml
<dependencies>
    <!-- Jedis（推荐） -->
    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>5.0.0</version>
    </dependency>
    
    <!-- Lettuce（Spring Boot 默认） -->
    <dependency>
        <groupId>io.lettuce</groupId>
        <artifactId>lettuce-core</artifactId>
        <version>6.3.0.RELEASE</version>
    </dependency>
    
    <!-- Redisson（分布式锁） -->
    <dependency>
        <groupId>org.redisson</groupId>
        <artifactId>redisson</artifactId>
        <version>3.24.3</version>
    </dependency>
</dependencies>
```

---

## 3. 数据类型

### 3.1 五大基本类型

| 类型 | 说明 | 应用场景 |
|------|------|----------|
| String | 字符串 | 缓存、计数器 |
| Hash | 哈希 | 对象存储 |
| List | 列表 | 消息队列、最新列表 |
| Set | 集合 | 去重、共同好友 |
| ZSet | 有序集合 | 排行榜 |

### 3.2 特殊类型

- **Bitmap**：位图（签到、状态标记）
- **HyperLogLog**：基数统计（UV 统计）
- **Geospatial**：地理位置（附近的人）
- **Stream**：流（消息队列）

---

## 4. 字符串操作

### 4.1 基本命令

```bash
# 设置值
SET key value
SET key value EX 60        # 60 秒过期
SET key value NX           # 仅当不存在
SET key value XX           # 仅当存在

# 获取值
GET key
MGET key1 key2 key3        # 批量获取

# 设置多个值
MSET key1 value1 key2 value2
MSETNX key1 value1 key2 value2  # 仅当都不存在

# 删除
DEL key

# 检查是否存在
EXISTS key

# 设置过期时间
EXPIRE key 60              # 60 秒后过期
PEXPIRE key 60000          # 60000 毫秒后过期
TTL key                    # 查看剩余时间（秒）
PTTL key                   # 查看剩余时间（毫秒）
PERSIST key                # 移除过期时间
```

### 4.2 数值操作

```bash
# 递增
INCR key                   # +1
INCRBY key 10              # +10
INCRBYFLOAT key 1.5        # +1.5

# 递减
DECR key                   # -1
DECRBY key 10              # -10

# 示例：计数器
SET views 0
INCR views                 # 1
INCRBY views 100           # 101
```

### 4.3 字符串操作

```bash
# 追加
APPEND key " additional"

# 获取长度
STRLEN key

# 获取子串
GETRANGE key 0 10          # 获取前 11 个字符
SETRANGE key 5 "world"     # 从第 5 位开始替换

# 位操作
SETBIT key 0 1             # 设置第 0 位为 1
GETBIT key 0               # 获取第 0 位
BITCOUNT key               # 统计 1 的数量
BITOP AND dest key1 key2   # 位运算
```

### 4.4 Java 示例

```java
import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

public class RedisStringExample {
    
    private static JedisPool pool;
    
    public static void init() {
        JedisPoolConfig config = new JedisPoolConfig();
        config.setMaxTotal(10);
        config.setMaxIdle(5);
        config.setMinIdle(1);
        
        pool = new JedisPool(config, "localhost", 6379, 2000, "password");
    }
    
    public static void stringOperations() {
        try (Jedis jedis = pool.getResource()) {
            // 基本操作
            jedis.set("name", "Alice");
            String name = jedis.get("name");
            
            // 带过期时间
            jedis.setex("session", 3600, "session_value");
            
            // 原子递增
            jedis.set("views", "0");
            long views = jedis.incr("views");
            
            // 批量操作
            jedis.mset("k1", "v1", "k2", "v2", "k3", "v3");
            List<String> values = jedis.mget("k1", "k2", "k3");
            
            // 分布式锁
            String lock = jedis.set("lock", "1", "NX", "EX", 10);
            if (lock != null) {
                try {
                    // 执行业务逻辑
                } finally {
                    jedis.del("lock");
                }
            }
        }
    }
}
```

---

## 5. 哈希操作

### 5.1 基本命令

```bash
# 设置字段
HSET user:1 name "Alice"
HSET user:1 age 25
HMSET user:1 name "Bob" age 30 city "Beijing"

# 获取字段
HGET user:1 name
HMGET user:1 name age city

# 获取所有字段
HGETALL user:1
HKEYS user:1               # 所有字段名
HVALS user:1               # 所有字段值

# 字段操作
HLEN user:1                # 字段数量
HEXISTS user:1 name        # 字段是否存在
HDEL user:1 age            # 删除字段

# 数值操作
HINCRBY user:1 score 10    # 字段递增
HINCRBYFLOAT user:1 score 1.5

# 示例
HSET user:1 name "Alice" age 25 email "alice@example.com"
HGET user:1 name           # "Alice"
HMGET user:1 name age      # ["Alice", "25"]
HGETALL user:1             # {name: "Alice", age: "25", email: "alice@example.com"}
```

### 5.2 Java 示例

```java
public class RedisHashExample {
    
    public static void hashOperations() {
        try (Jedis jedis = pool.getResource()) {
            // 设置哈希
            jedis.hset("user:1", "name", "Alice");
            jedis.hset("user:1", "age", "25");
            
            // 批量设置
            Map<String, String> user = new HashMap<>();
            user.put("name", "Bob");
            user.put("age", "30");
            user.put("city", "Beijing");
            jedis.hmset("user:2", user);
            
            // 获取
            String name = jedis.hget("user:1", "name");
            List<String> info = jedis.hmget("user:1", "name", "age");
            Map<String, String> all = jedis.hgetAll("user:1");
            
            // 字段操作
            Boolean exists = jedis.hexists("user:1", "name");
            Long len = jedis.hlen("user:1");
            Set<String> keys = jedis.hkeys("user:1");
            List<String> values = jedis.hvals("user:1");
            
            // 递增
            jedis.hincrBy("user:1", "score", 10);
            Double score = jedis.hincrByFloat("user:1", "score", 1.5);
            
            // 删除
            jedis.hdel("user:1", "age");
        }
    }
    
    // 存储对象示例
    public static void storeObject() {
        try (Jedis jedis = pool.getResource()) {
            User user = new User("1", "Alice", 25, "alice@example.com");
            
            // 方式 1：Hash 存储
            jedis.hset("user:" + user.getId(), 
                      BeanUtils.describe(user));
            
            // 方式 2：JSON 存储
            jedis.set("user:" + user.getId(), 
                     JSON.toJSONString(user));
        }
    }
}
```

---

## 6. 列表操作

### 6.1 基本命令

```bash
# 左侧推送（头）
LPUSH list a b c             # list: [c, b, a]

# 右侧推送（尾）
RPUSH list d e f             # list: [c, b, a, d, e, f]

# 左侧弹出
LPOP list                    # 返回 c

# 右侧弹出
RPOP list                    # 返回 f

# 获取元素
LINDEX list 0                # 获取第一个
LRANGE list 0 -1             # 获取所有
LLEN list                    # 列表长度

# 设置元素
LSET list 0 "new"            # 设置第一个元素

# 插入元素
LINSERT list BEFORE "a" "x"  # 在"a"前插入"x"
LINSERT list AFTER "a" "y"   # 在"a"后插入"y"

# 删除元素
LREM list 2 "a"              # 删除 2 个"a"

# 裁剪列表
LTRIM list 0 10              # 保留前 11 个元素

# 阻塞操作（消息队列）
BLPOP list1 list2 10         # 阻塞 10 秒等待左侧弹出
BRPOP list1 list2 10         # 阻塞 10 秒等待右侧弹出

# 示例：消息队列
RPUSH queue "msg1" "msg2" "msg3"
LPOP queue                   # 消费者获取消息
```

### 6.2 Java 示例

```java
public class RedisListExample {
    
    // 消息队列 - 生产者
    public static void produce(String queue, String message) {
        try (Jedis jedis = pool.getResource()) {
            jedis.rpush(queue, message);
        }
    }
    
    // 消息队列 - 消费者
    public static String consume(String queue) {
        try (Jedis jedis = pool.getResource()) {
            // 阻塞获取消息
            List<String> result = jedis.blpop(0, queue);
            return result != null ? result.get(1) : null;
        }
    }
    
    // 最新列表（如最新微博）
    public static void addLatestPost(String userId, String postId) {
        try (Jedis jedis = pool.getResource()) {
            String key = "user:" + userId + ":posts";
            jedis.lpush(key, postId);
            jedis.ltrim(key, 0, 99);  // 只保留最新 100 条
        }
    }
    
    // 获取最新列表
    public static List<String> getLatestPosts(String userId, int count) {
        try (Jedis jedis = pool.getResource()) {
            String key = "user:" + userId + ":posts";
            return jedis.lrange(key, 0, count - 1);
        }
    }
}
```

---

## 7. 集合操作

### 7.1 基本命令

```bash
# 添加元素
SADD set a b c d

# 删除元素
SREM set c d

# 获取所有元素
SMEMBERS set

# 检查元素
SISMEMBER set a            # 1 或 0

# 集合大小
SCARD set

# 随机获取
SRANDMEMBER set 5          # 随机 5 个（不删除）
SPOP set 3                 # 随机弹出 3 个

# 集合运算
SUNION set1 set2           # 并集
SINTER set1 set2           # 交集
SDIFF set1 set2            # 差集（set1 - set2）

# 存储运算结果
SUNIONSTORE dest set1 set2
SINTERSTORE dest set1 set2
SDIFFSTORE dest set1 set2

# 示例：共同好友
SINTER user:1:friends user:2:friends
```

### 7.2 Java 示例

```java
public class RedisSetExample {
    
    // 添加标签
    public static void addTags(String itemId, Set<String> tags) {
        try (Jedis jedis = pool.getResource()) {
            String key = "item:" + itemId + ":tags";
            jedis.sadd(key, tags.toArray(new String[0]));
        }
    }
    
    // 获取标签
    public static Set<String> getTags(String itemId) {
        try (Jedis jedis = pool.getResource()) {
            String key = "item:" + itemId + ":tags";
            return jedis.smembers(key);
        }
    }
    
    // 共同好友
    public static Set<String> getMutualFriends(String user1, String user2) {
        try (Jedis jedis = pool.getResource()) {
            String key1 = "user:" + user1 + ":friends";
            String key2 = "user:" + user2 + ":friends";
            return jedis.sinter(key1, key2);
        }
    }
    
    // 随机抽奖
    public static Set<String> luckyDraw(String activityKey, int count) {
        try (Jedis jedis = pool.getResource()) {
            return jedis.spop(activityKey, count);
        }
    }
    
    // 签到（Bitmap 更优）
    public static void checkIn(String userId, String date) {
        try (Jedis jedis = pool.getResource()) {
            String key = "user:" + userId + ":checkin";
            jedis.sadd(key, date);
        }
    }
}
```

---

## 8. 有序集合

### 8.1 基本命令

```bash
# 添加元素
ZADD zset 1 "a"
ZADD zset 2 "b" 3 "c"
ZADD zset NX 4 "d"         # 仅当不存在
ZADD zset XX 5 "a"         # 仅当存在

# 获取元素
ZCARD zset                 # 元素数量
ZRANGE zset 0 -1           # 按分数升序
ZREVRANGE zset 0 -1        # 按分数降序
ZRANGE zset 0 -1 WITHSCORES

# 按分数范围
ZRANGEBYSCORE zset 1 10
ZREVRANGEBYSCORE zset 10 1

# 按排名范围
ZRANGE zset 0 9            # 前 10 名

# 获取分数
ZSCORE zset "a"
ZRANK zset "a"             # 升序排名
ZREVRANK zset "a"          # 降序排名

# 删除
ZREM zset "a"
ZREMRANGEBYRANK zset 0 9   # 删除前 10 名
ZREMRANGEBYSCORE zset 1 10 # 删除分数 1-10

# 递增
ZINCRBY zset 1 "a"

# 集合运算
ZUNIONSTORE dest 2 zset1 zset2
ZINTERSTORE dest 2 zset1 zset2

# 示例：排行榜
ZADD leaderboard 100 "player1"
ZADD leaderboard 200 "player2"
ZREVRANGE leaderboard 0 9 WITHSCORES  # 前 10 名
```

### 8.2 Java 示例

```java
public class RedisZSetExample {
    
    // 添加排行榜分数
    public static void addScore(String leaderboard, String playerId, double score) {
        try (Jedis jedis = pool.getResource()) {
            jedis.zadd(leaderboard, score, playerId);
        }
    }
    
    // 批量添加
    public static void addScores(String leaderboard, Map<String, Double> scores) {
        try (Jedis jedis = pool.getResource()) {
            jedis.zadd(leaderboard, scores);
        }
    }
    
    // 获取 Top N
    public static Set<Tuple> getTopN(String leaderboard, int n) {
        try (Jedis jedis = pool.getResource()) {
            return jedis.zrevrangeWithScores(leaderboard, 0, n - 1);
        }
    }
    
    // 获取玩家排名
    public static Long getRank(String leaderboard, String playerId) {
        try (Jedis jedis = pool.getResource()) {
            return jedis.zrevrank(leaderboard, playerId);
        }
    }
    
    // 获取玩家分数
    public static Double getScore(String leaderboard, String playerId) {
        try (Jedis jedis = pool.getResource()) {
            return jedis.zscore(leaderboard, playerId);
        }
    }
    
    // 获取分数范围
    public static Set<Tuple> getScoreRange(String leaderboard, 
        double minScore, double maxScore) {
        try (Jedis jedis = pool.getResource()) {
            return jedis.zrevrangeByScoreWithScores(leaderboard, 
                maxScore, minScore);
        }
    }
    
    // 删除玩家
    public static Long removePlayer(String leaderboard, String playerId) {
        try (Jedis jedis = pool.getResource()) {
            return jedis.zrem(leaderboard, playerId);
        }
    }
}
```

---

## 9. 高级特性

### 9.1 发布订阅

```bash
# 订阅频道
SUBSCRIBE channel1 channel2

# 发布消息
PUBLISH channel1 "Hello"

# 按模式订阅
PSUBSCRIBE news.* sports.*

# 示例
# 终端 1
SUBSCRIBE chat:room1

# 终端 2
PUBLISH chat:room1 "Hello everyone!"
```

### 9.2 事务

```bash
# 基本事务
MULTI
SET key1 "value1"
SET key2 "value2"
EXEC

# 监视键（乐观锁）
WATCH key1
MULTI
SET key1 "new_value"
EXEC  # 如果 key1 被修改，事务失败

# 取消事务
DISCARD
```

### 9.3 Lua 脚本

```bash
# 执行脚本
EVAL "return redis.call('SET', KEYS[1], ARGV[1])" 1 key1 value1

# 加载脚本
SCRIPT LOAD "return 1"
EVALSHA <sha1> 1 key1

# 示例：原子递增并返回
EVAL "
local key = KEYS[1]
local limit = tonumber(ARGV[1])
local current = tonumber(redis.call('GET', key) or '0')
if current < limit then
    redis.call('INCR', key)
    return 1
else
    return 0
end
" 1 rate_limit:user:1 100
```

### 9.4 持久化

```bash
# RDB 快照
SAVE                     # 阻塞保存
BGSAVE                   # 后台保存

# AOF 重写
BGREWRITEAOF

# 查看持久化状态
INFO persistence
```

### 9.5 主从复制

```bash
# 配置从节点
# redis.conf
replicaof 192.168.1.100 6379
masterauth your_password

# 动态配置
SLAVEOF 192.168.1.100 6379
SLAVEOF NO ONE           # 提升为主节点

# 查看复制状态
INFO replication
```

### 9.6 哨兵模式

```bash
# sentinel.conf
sentinel monitor mymaster 192.168.1.100 6379 2
sentinel down-after-milliseconds mymaster 5000
sentinel failover-timeout mymaster 60000
sentinel parallel-syncs mymaster 1

# 启动哨兵
redis-sentinel sentinel.conf

# 获取主节点
redis-cli -p 26379 sentinel get-master-addr-by-name mymaster
```

### 9.7 集群

```bash
# 创建集群
redis-cli --cluster create \
  127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002 \
  127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005 \
  --cluster-replicas 1

# 检查集群
redis-cli --cluster check 127.0.0.1:7000

# 重新分片
redis-cli --cluster reshard 127.0.0.1:7000

# 添加节点
redis-cli --cluster add-node 127.0.0.1:7006 127.0.0.1:7000
```

---

## 10. 实战示例

### 10.1 缓存示例

```java
public class RedisCacheExample {
    
    // 缓存穿透解决方案
    public static User getUserWithCache(String userId) {
        try (Jedis jedis = pool.getResource()) {
            String key = "user:" + userId;
            
            // 1. 从缓存获取
            String cached = jedis.get(key);
            if (cached != null) {
                if ("NULL".equals(cached)) {
                    return null;  // 缓存空值，防止穿透
                }
                return JSON.parseObject(cached, User.class);
            }
            
            // 2. 从数据库获取
            User user = userDao.getById(userId);
            
            // 3. 写入缓存
            if (user != null) {
                jedis.setex(key, 3600, JSON.toJSONString(user));
            } else {
                jedis.setex(key, 300, "NULL");  // 空值缓存 5 分钟
            }
            
            return user;
        }
    }
    
    // 缓存雪崩解决方案
    public static void cacheWithRandomTTL(String key, String value) {
        try (Jedis jedis = pool.getResource()) {
            // 随机过期时间，避免同时失效
            int ttl = 3600 + new Random().nextInt(600);
            jedis.setex(key, ttl, value);
        }
    }
    
    // 热点数据永不过期
    public static void cacheHotData(String key, String value) {
        try (Jedis jedis = pool.getResource()) {
            jedis.set(key, value);
            // 后台异步更新
        }
    }
}
```

### 10.2 分布式锁

```java
public class RedisLockExample {
    
    // 简单分布式锁
    public static boolean tryLock(String lockKey, String requestId, int expireTime) {
        try (Jedis jedis = pool.getResource()) {
            String result = jedis.set(lockKey, requestId, "NX", "EX", expireTime);
            return "OK".equals(result);
        }
    }
    
    // 释放锁（使用 Lua 脚本保证原子性）
    public static boolean unlock(String lockKey, String requestId) {
        try (Jedis jedis = pool.getResource()) {
            String script = 
                "if redis.call('get', KEYS[1]) == ARGV[1] then " +
                "   return redis.call('del', KEYS[1]) " +
                "else " +
                "   return 0 " +
                "end";
            
            Object result = jedis.eval(script, 
                Collections.singletonList(lockKey),
                Collections.singletonList(requestId));
            
            return Long.valueOf(1).equals(result);
        }
    }
    
    // 使用示例
    public static void doWithLock(String lockKey, Runnable task) {
        String requestId = UUID.randomUUID().toString();
        boolean locked = false;
        
        try {
            // 尝试获取锁
            locked = tryLock(lockKey, requestId, 10);
            
            if (locked) {
                task.run();
            } else {
                throw new RuntimeException("获取锁失败");
            }
        } finally {
            if (locked) {
                unlock(lockKey, requestId);
            }
        }
    }
    
    // Redisson 分布式锁（推荐）
    public static void redissonLock() {
        Config config = new Config();
        config.useSingleServer().setAddress("redis://localhost:6379");
        
        RedissonClient redisson = Redisson.create(config);
        RLock lock = redisson.getLock("myLock");
        
        try {
            lock.lock();
            // 业务逻辑
        } finally {
            lock.unlock();
        }
        
        redisson.shutdown();
    }
}
```

### 10.3 限流器

```java
public class RedisRateLimiter {
    
    // 固定窗口限流
    public static boolean isAllowed(String key, int limit, int windowSeconds) {
        try (Jedis jedis = pool.getResource()) {
            String windowKey = key + ":" + (System.currentTimeMillis() / 1000 / windowSeconds);
            
            long count = jedis.incr(windowKey);
            if (count == 1) {
                jedis.expire(windowKey, windowSeconds);
            }
            
            return count <= limit;
        }
    }
    
    // 滑动窗口限流
    public static boolean isAllowedSliding(String key, int limit, int windowSeconds) {
        try (Jedis jedis = pool.getResource()) {
            ZSetParams params = new ZSetParams().nx();
            long now = System.currentTimeMillis();
            long windowStart = now - windowSeconds * 1000;
            
            // 删除过期元素
            jedis.zremrangeByScore(key, 0, windowStart);
            
            // 统计当前窗口数量
            long count = jedis.zcard(key);
            
            if (count < limit) {
                jedis.zadd(key, params, now, String.valueOf(now));
                jedis.expire(key, windowSeconds);
                return true;
            }
            
            return false;
        }
    }
    
    // 令牌桶限流
    public static boolean tryAcquire(String key, int capacity, int refillRate) {
        try (Jedis jedis = pool.getResource()) {
            String script = 
                "local key = KEYS[1] " +
                "local capacity = tonumber(ARGV[1]) " +
                "local refill_rate = tonumber(ARGV[2]) " +
                "local now = tonumber(ARGV[3]) " +
                "local requested = tonumber(ARGV[4]) " +
                "" +
                "local last_tokens = tonumber(redis.call('HGET', key, 'tokens') or capacity) " +
                "local last_refreshed = tonumber(redis.call('HGET', key, 'refreshed') or now) " +
                "" +
                "local delta = math.max(0, now - last_refreshed) " +
                "local filled = math.floor(delta * refill_rate) " +
                "local tokens = math.min(capacity, last_tokens + filled) " +
                "" +
                "if tokens >= requested then " +
                "   tokens = tokens - requested " +
                "   redis.call('HSET', key, 'tokens', tokens) " +
                "   redis.call('HSET', key, 'refreshed', now) " +
                "   return 1 " +
                "else " +
                "   return 0 " +
                "end";
            
            long now = System.currentTimeMillis();
            long result = (Long) jedis.eval(script, 
                Collections.singletonList(key),
                Arrays.asList(String.valueOf(capacity), 
                             String.valueOf(refillRate),
                             String.valueOf(now),
                             "1"));
            
            return result == 1;
        }
    }
}
```

### 10.4 签到系统

```java
public class CheckInSystem {
    
    // 使用 Bitmap 签到
    public static void checkIn(String userId, LocalDate date) {
        try (Jedis jedis = pool.getResource()) {
            String key = "checkin:" + userId + ":" + date.getYear() + date.getMonthValue();
            int day = date.getDayOfMonth();
            jedis.setbit(key, day - 1, true);
        }
    }
    
    // 检查是否签到
    public static boolean isCheckedIn(String userId, LocalDate date) {
        try (Jedis jedis = pool.getResource()) {
            String key = "checkin:" + userId + ":" + date.getYear() + date.getMonthValue();
            int day = date.getDayOfMonth();
            return jedis.getbit(key, day - 1);
        }
    }
    
    // 统计签到次数
    public static long getCheckInCount(String userId, int year, int month) {
        try (Jedis jedis = pool.getResource()) {
            String key = "checkin:" + userId + ":" + year + month;
            return jedis.bitcount(key);
        }
    }
    
    // 连续签到天数
    public static int getContinuousCheckInDays(String userId, LocalDate endDate) {
        try (Jedis jedis = pool.getResource()) {
            int continuous = 0;
            LocalDate date = endDate;
            
            while (true) {
                String key = "checkin:" + userId + ":" + date.getYear() + date.getMonthValue();
                int day = date.getDayOfMonth();
                
                if (jedis.getbit(key, day - 1)) {
                    continuous++;
                    date = date.minusDays(1);
                } else {
                    break;
                }
            }
            
            return continuous;
        }
    }
}
```

### 10.5 UV 统计

```java
public class UVStatistics {
    
    // 使用 HyperLogLog 统计 UV
    public static void recordVisit(String pageKey, String userId) {
        try (Jedis jedis = pool.getResource()) {
            jedis.pfadd("uv:" + pageKey, userId);
        }
    }
    
    // 获取 UV
    public static long getUV(String pageKey) {
        try (Jedis jedis = pool.getResource()) {
            return jedis.pfcount("uv:" + pageKey);
        }
    }
    
    // 合并多个页面的 UV
    public static long getMergedUV(String... pageKeys) {
        try (Jedis jedis = pool.getResource()) {
            String[] keys = Arrays.stream(pageKeys)
                .map(k -> "uv:" + k)
                .toArray(String[]::new);
            return jedis.pfcount(keys);
        }
    }
}
```

---

## 附录：常用命令

```bash
# 查看信息
INFO
INFO memory
INFO stats

# 监控
MONITOR

# 慢查询
SLOWLOG GET 10
SLOWLOG LEN
SLOWLOG RESET

# 客户端
CLIENT LIST
CLIENT KILL <ip:port>

# 内存分析
MEMORY USAGE key
MEMORY STATS

# 键操作
KEYS pattern           # 生产环境慎用
SCAN 0 MATCH pattern COUNT 100
TYPE key
RENAME key newkey
MOVE key db

# 数据库操作
SELECT 0
DBSIZE
FLUSHDB
FLUSHALL

# 性能测试
redis-benchmark -q -n 100000
redis-benchmark -q -n 100000 -P 10
```

---

## 学习资源

- **官方文档**: https://redis.io/documentation
- **Redis 命令参考**: https://redis.io/commands
- **Redis 设计与实现**: http://redisbook.com/

---

**祝你 Redis 学习愉快！** 🚀

*作者：JOJO*
