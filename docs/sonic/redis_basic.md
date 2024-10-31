
## install_config

[Redis 配置 | 菜鸟教程 (runoob.com)](https://www.runoob.com/redis/redis-conf.html)
```
sudo apt install redis-server
redis-server

echo vm.overcommit_memory = 1 >> /etc/sysctl.conf
echo madvise >>/etc/sysctl.conf

redis-server --bind 0.0.0.0 --port 7777
redis-cli -h 127.0.0.1 -p 7777

```

### data_type
- string（字符串）:基本的数据存储单元，可以存储字符串、整数或者浮点数。
	-  `SET key value`：设置键的值。
	- `GET key`：获取键的值。
	- `INCR key`：将键的值加 1。
	- `DECR key`：将键的值减 1。
	- `APPEND key value`：将值追加到键的值之后。
- hash（哈希）:一个键值对集合，可以存储多个字段。
	 - `HSET key field value`：设置哈希表中字段的值。
	 - **HMSET** 设置了两个 field=>value 对
	- HGET key field`：获取哈希表中字段的值。
	- `HGETALL key`：获取哈希表中所有字段和值。
	- `HDEL key field`：删除哈希表中的一个或多个字段。
```

		redis 127.0.0.1:6379> DEL runoob
		redis 127.0.0.1:6379> HMSET runoob field1 "Hello" field2 "World"
		"OK"
		redis 127.0.0.1:6379> HGET runoob field1
		"Hello"
		192.168.2.105:7777> hgetall runoob
		1) "field1"
		2) "Hello"
		3) "field2"
		4) "World"
```

- list（列表）:一个简单的列表，可以存储一系列的字符串元素。
	- `LPUSH key value`：将值插入到列表头部。
	- `RPUSH key value`：将值插入到列表尾部。
	- `LPOP key`：移出并获取列表的第一个元素。
	- `RPOP key`：移出并获取列表的最后一个元素。
	- `LRANGE key start stop`：获取列表在指定范围内的元素。
```
redis 127.0.0.1:6379> DEL runoob
redis 127.0.0.1:6379> lpush runoob redis
(integer) 1
redis 127.0.0.1:6379> lpush runoob mongodb
(integer) 2
redis 127.0.0.1:6379> lpush runoob rabbitmq
(integer) 3
redis 127.0.0.1:6379> lrange runoob 0 10
1) "rabbitmq"
2) "mongodb"
3) "redis"
redis 127.0.0.1:6379>
```
- set（集合）:一个无序集合，可以存储不重复的字符串元素。
	- `SADD key value`：向集合添加一个或多个成员。
	- `SREM key value`：移除集合中的一个或多个成员。
	- `SMEMBERS key`：返回集合中的所有成员。
	- `SISMEMBER key value`：判断值是否是集合的成员。
- zset(sorted set：有序集合): 类似于集合，但是每个元素都有一个分数（score）与之关联。
	- `ZADD key score value`：向有序集合添加一个或多个成员，或更新已存在成员的分数。
	- `ZRANGE key start stop [WITHSCORES]`：返回指定范围内的成员。
	- `ZREM key value`：移除有序集合中的一个或多个成员。
	- `ZSCORE key value`：返回有序集合中，成员的分数值。
- 位图（Bitmaps）：基于字符串类型，可以对每个位进行操作。
- 超日志（HyperLogLogs）：用于基数统计，可以估算集合中的唯一元素数量。
- 地理空间（Geospatial）：用于存储地理位置信息。
- 发布/订阅（Pub/Sub）：一种消息通信模式，允许客户端订阅消息通道，并接收发布到该通道的消息。
	- [PSUBSCRIBE pattern [pattern ...]](https://www.runoob.com/redis/pub-sub-psubscribe.html)  订阅一个或多个符合给定模式的频道。
	- [PUBSUB subcommand [argument [argument ...]]](https://www.runoob.com/redis/pub-sub-pubsub.html).查看订阅与发布系统状态。
	- [PUBLISH channel message](https://www.runoob.com/redis/pub-sub-publish.html)将信息发送到指定的频道。
	- [SUBSCRIBE channel](https://www.runoob.com/redis/pub-sub-subscribe.html)订阅给定的一个或多个频道的信息。
	- [UNSUBSCRIBE [channel [channel ...]]](https://www.runoob.com/redis/pub-sub-unsubscribe.html)指退订给定的频道。
- 流（Streams）：用于消息队列和日志存储，支持消息的持久化和时间排序。
- 模块（Modules）：Redis 支持动态加载模块，可以扩展 Redis 的功能。

### server

- auth password
- echo message
- ping 
- quit
- select index 切换到指定的数据库
- info
- bgrewriteaof 异步执行一个 AOF（AppendOnly File） 文件重写操作
- bgsave 后台异步保存当前数据库的数据到磁盘
- client kill \[ip:port id] \[client-id] 关闭客户端连接
- client list
- client getname
- client pause timeout
- client setname
- save 步保存数据到硬盘
- sync 复制功能(replication)的内部命令
- CONFIG GET databases
- INFO keyspace


### database
`Redis` 默认提供了`16`个数据库. 每个数据库都有一个`id`, 从 `0` 开始, [0,15]。 不同的数据库中数据隔离保存。我们可以通过修改redis的配置文件进行修改数据库的数量。

```text
database 32
```

- 使用 `select <ID>` 可以切换数据库.
- 使用 `flushdb`只能删除 **该数据库(一个库)** 中的数据。
- 使用 `flushall` 可以删除 **所有库** 中的数据。


### python3

[Python redis 使用介绍 | 菜鸟教程 (runoob.com)](https://www.runoob.com/w3cnote/python-redis-intro.html)

`sudo apt install python3-redis`
`pip3 install redis`

```

>>> import redis
>>> r = redis.StrictRedis(host='localhost', port=6379, db=0)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
'bar'

```

