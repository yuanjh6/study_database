# 数据库05redis常用命令整理
## redis启动
```
redis-server            #启动服务端
redis-cli redis.conf    #启动客户端
redis-cli  -h  {host}  -p  {port}  -a  {password}    #配置客户端启动的主机和端口
```


## 获取redis配置信息
| 序号 |                 配置项                  |                                                                                    说明                                                                                    |
| ---- | -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1    | ****daemonize**** no                   | 配置Redis是否以守护线程的方式启动,默认为NO                                                                                                                                       |
| 2    | ****port**** 6379                      | Redis 监听端口，默认为 6379                                                                                                                                                  |
| 3    | ****bind**** 127.0.0.1                 | 绑定的主机地址                                                                                                                                                               |
| 4    | ****timeout**** 300                    | 客户端闲置多少秒后关闭连接，如果为0，表示关闭该功能                                                                                                                                 |
| 5    | ****loglevel**** notice                | 日志记录级别，总共四个级别：debug、verbose、notice、warning，默认为 notice                                                                                                         |
| 6    | ****databases**** 16                   | 设置数据库的数量,默认16个库(0-15)，默认初始使用的库为0，可以使用SELECT 命令在连接上指定数据库id                                                                                           |
| 7    | ****save****<seconds> <changes>         | 在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合。Redis 配置文件默认3个条件:save 900 1     900秒内有1个更改save 300 10    300秒内有10个更改save 60 10000  60秒内有10000个更改 |
| 8    | ****rdbcompression**** yes              | 存储至本地数据库时是否压缩数据，默认为 yes，Redis 采用 LZF 压缩，如果为了节省 CPU 时间，可以关闭该选项，但会导致数据库文件变的巨大                                                               |
| 9    | ****dbfilename**** dump.rdb             | 本地数据库文件名，默认值为 dump.rdb                                                                                                                                            |
| 10   | ****appendonly**** no                  | 是否在每次更新操作后进行日志记录，默认为 no                                                                                                                                       |
| 11   | ****appendfilename**** appendonly.aof   | 指定更新日志文件名，默认为 appendonly.aof                                                                                                                                       |
| 12   | ****appendfsync**** everysec            | 异步更新日志条件：no：表示等操作系统进行数据缓存同步到磁盘（快）always：表示每次更新操作后手动调用 fsync() 将数据写到磁盘（慢，安全）everysec：表示每秒同步一次（折中，默认值）                          |
| 13   | ****dir**** ./                         | 指定本地数据库存放目录                                                                                                                                                        |
| 14   | ****slaveof**** <masterip> <masterport> | 当本机为 slave 时，设置 master 的 IP 地址及端口                                                                                                                                 |

## Redis 连接命令

下表列出了 redis 连接的基本命令：

```
序号	命令及描述  
1	AUTH password 验证密码是否正确  
2	ECHO message 打印字符串  
3	PING 查看服务是否运行  
4	QUIT 关闭当前连接  
5	SELECT index 切换到指定的数据库  
```

## 数据类型
```
String:可以包含任何数据,比如jpg图片或者序列化的对象,一个键最大能存储512M  
Hash:适合存储对象,并且可以像数据库中update一个属性一样只修改某一项属性值(Memcached中需要取出整个字符串反序列化成对象修改完再序列化存回去)
List:增删快,提供了操作某一段元素的API
Set:1,添加、删除,查找的复杂度都是O(1).2、为集合提供了求交集、并集、差集等操作
Sorted Set:数据插入集合时,已经进行天然排序
```

## Redis keys 命令
```
keys 命令
?    匹配一个字符
*    匹配任意个（包括0个）字符
[]    匹配括号间的任一个字符，可以使用 "-" 符号表示一个范围，如 a[b-d] 可以匹配 "ab","ac","ad"
\x    匹配字符x，用于转义符号，如果要匹配 "?" 就需要使用 \?

keys * 查询当前库的所有键
exists 判断某个键是否存在
type 查看键的类型
del 删除某个键
expire 为键值设置过期时间，单位秒。
```
ttl key

作用： 以秒为单位 返回 key 的剩余生存时间

返回值： -1：key永不过期

-2: key 不存在

正整数： key 的剩余存在时间（秒）


## 字符串命令
一个key对应一个value。一个键最大能存储512MB。string类型是二进制安全的。

```
incr key    #递增数字
当存储的字符串是整数形式时，redis提供了一个使用的命令 incr 作用是让当前的键值递增，并返回递增后的值
当要操作的键不存在时会默认键值为 0  ，所以第一次递增后的结果是 1 ，当键值不是整数时 redis会提示错误

incrby key increment    #增加指定的整数

decr key                #desc 命令与incr 命令用法相同，只不过是让键值递减
decrby key increment    #decrby 命令与 incrby命令用法相同

incrbyfloat key increment #命令类似 incrby 命令，差别是前者可以递增一个双精度浮点数
```
（1）set key value [ex 秒数] [px 毫秒数] [nx/xx]

&emsp;如果ex和px同时写，则以后面的有效期为准

&emsp;nx：如果key不存在则建立

&emsp;xx：如果key存在则修改其值

（2）get key：取值

（3）mset key1 value1 key2 value2 一次设置多个值

（4）mget key1 key2 ：一次获取多个值

（5）setrange key offset value：把字符串的offset偏移字节改成value

&emsp;如果偏移量 > 字符串长度，该字符自动补0x00

（6）append key value ：把value追加到key 的原值上

（7）getrange key start stop：获取字符串中[start, stop]范围的值

&emsp;对于字符串的下标，左数从0开始，右数从-1开始

&emsp;注意：

&emsp;当start>length，则返回空字符串

&emsp;当stop>=length，则截取至字符串尾

&emsp;如果start所处位置在stop右边，则返回空字符串

（8）getset key nrevalue：获取并返回旧值，在设置新值


##  hash（哈希）　
Redis hash 是一个 string 类型的 field 和 value 的映射表，hash 特别适合用于存储对象。每个 hash 可以存储 232 - 1 键值对（40多亿）。

（1）hset myhash field value：设置myhash的field为value

（2）hsetnx myhash field value：不存在的情况下设置myhash的field为value

（3）hmset myhash field1 value1 field2 value2：同时设置多个field

（4）hget myhash field：获取指定的hash field

（5）hmget myhash field1 field2：一次获取多个field

（6）hincrby myhash field 5：指定的hash field加上给定的值

（7）hexists myhash field：测试指定的field是否存在

（8）hlen myhash：返回hash的field数量

（9）hdel myhash field：删除指定的field

（10）hkeys myhash：返回hash所有的field

（11）hvals myhash：返回hash所有的value

（12）hgetall myhash：获取某个hash中全部的field及value


## list（列表）
Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。列表最多可存储 232 - 1 元素 (4294967295, 每个列表可存储40多亿)。

（1）lpush key value：把值插入到链表头部

（2）rpush key value：把值插入到链表尾部

（3）lpop key ：返回并删除链表头部元素

（4）rpop key： 返回并删除链表尾部元素

（5）lrange key start stop：返回链表中[start, stop]中的元素

（6）lrem key count value：从链表中删除value值，删除count的绝对值个value后结束

&emsp;count > 0 从表头删除

&emsp;count < 0 从表尾删除

&emsp;count=0 全部删除

（7）ltrim key start stop：剪切key对应的链接，切[start, stop]一段并把改制重新赋给key

（8）lindex key index：返回index索引上的值


## set（集合）
Redis的Set是string类型的无序集合。值不重复。

（1）sadd key value1 value2：往集合里面添加元素

（2）smembers key：获取集合所有的元素

（3）srem key value：删除集合某个元素

（4）spop key：返回并删除集合中1个随机元素（可以坐抽奖，不会重复抽到某人）

（5）srandmember key：随机取一个元素

（6）sismember key value：判断集合是否有某个值

（7）scard key：返回集合元素的个数

（8）smove source dest value：把source的value移动到dest集合中

（9）sinter key1 key2 key3：求key1 key2 key3的交集

（10）sunion key1 key2：求key1 key2 的并集

（11）sdiff key1 key2：求key1 key2的差集

（12）sinterstore res key1 key2：求key1 key2的交集并存在res里


## zset（sorted set：有序集合）

Redis zset 和 set 一样也是string类型元素的集合。且不允许重复的成员。不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。zset的成员是唯一的,但分数(score)却可以重复。

（1）zadd key score1 value1：添加元素

（2）zrange key start stop [withscore]：把集合排序后,返回名次[start,stop]的元素

&emsp;默认是升续排列 withscores 是把score也打印出来

（3）zrank key member：查询member的排名（升序0名开始）

（4）zrangebyscore key min max [withscores] limit offset N：集合（升序）

&emsp;排序后取score在[min, max]内的元素，并跳过offset个，取出N个

（5）zrevrank key member：查询member排名（降序 0名开始）

（6）zremrangebyscore key min max：按照score来删除元素，删除score在[min, max]之间

（7）zrem key value1 value2：删除集合中的元素

（8）zremrangebyrank key start end：按排名删除元素，删除名次在[start, end]之间的

（9）zcard key：返回集合元素的个数

（10）zcount key min max：返回[min, max]区间内元素数量


## 服务器相关命令
（1）ping：测定连接是否存活

（2）echo：在命令行打印一些内容

（3）select：选择数据库

（4）quit：退出连接

（5）dbsize：返回当前数据库中key的数目

（6）info：获取服务器的信息和统计

（7）monitor：实时转储收到的请求

（8）config get 配置项：获取服务器配置的信息

&emsp;config set 配置项  值：设置配置项信息

（9）flushdb：删除当前选择数据库中所有的key

（10）flushall：删除所有数据库中的所有的key

（11）time：显示服务器时间，时间戳（秒），微秒数

（12）bgrewriteaof：后台保存rdb快照

（13）bgsave：后台保存rdb快照

（14）save：保存rdb快照

（15）lastsave：上次保存时间

（16）shutdown [save/nosave]

&emsp;注意：如果不小心运行了flushall，立即shutdown nosave，关闭服务器，然后手工编辑aof文件，去掉文件中的flushall相关行，然后开启服务器，就可以倒回原来是数据。如果flushall之后，系统恰好bgwriteaof了，那么aof就清空了，数据丢失。

（17）showlog：显示慢查询

&emsp;问：多慢才叫慢？

&emsp;答：由slowlog-log-slower-than 10000，来指定（单位为微秒）

&emsp;问：服务器存储多少条慢查询记录

&emsp;答：由slowlog-max-len 128，来做限制



## 主从复制
master可以有多个slave，slave还可以连接到其他slave。

主从复制不会阻塞master，在数据同步时，master可以继续处理client请求


## 事务控制
注意：redis 部分事务失败，不会回滚全部事务


## 持久化机制
(1)自动保存快照

配置信息：

```
save 900 1 #如果900秒内超过1个key被修改，则发起快照保存
save 300 10 #如果300秒内超过1个key被修改，则发起快照保存
save 60 10000 …
```

1. redis调用fork，为主进程（父进程）创建一个子进程

2. 父进程继续处理client请求，子进程将内存内容写入临时文件。子进程地址空间内的数据是fork时的整个数据库的快照。子进程不会影响父进程处理client请求。

3.当子进程将快照写入临时文件完毕后，用临时文件替换原来的快照文件，然后子进程退出。



(2)save / bgsave

 手动保存快照，在主线程中完成快照保存，会阻塞所有的client请求。

 每次都是将内存数据完整的写入到磁盘，如果数据量大，会引起大量的IO操作，影响性能。

Append-only file方式

 记录每次write的操作内容

 比快照方式更好



## 发布及订阅消息pub/sub


## 使用场景
1.字符串

缓存功能、计数、共享session、限速

2.哈希

hash特别适合用于存储对象。

存储部分变更的数据，如用户信息等。

3.列表

lpush+lpop=stock(栈)

lpush+rpop=queue(队列)

lpush+ltrim=Capped Collection (有限集合)

lpush+brpop=Message Queue(消息队列)

4.集合 
sadd=Tagging(标签)

spop/srandmember=Random item (生成随机数，比如抽奖)

sadd+sinter=Social Graph(社交需求)

5.有序集合

排行榜

## 参考
Redis常用命令整理：https://blog.csdn.net/weixin_42389216/article/details/106792627

Redis常用命令整理：https://blog.csdn.net/weixin_42714605/article/details/91413847

redis常用命令整理：https://www.jb51.net/article/182067.htm

redis 常用命令总结：https://blog.csdn.net/qq_38174263/article/details/80009943

Redis常用命令整理：https://www.cnblogs.com/seizedays/p/12493036.html

redis常用命令及使用场景：https://www.cnblogs.com/alphathink/p/10749626.html

Redis基本命令整理:https://blog.csdn.net/weixin_33834628/article/details/89442998  