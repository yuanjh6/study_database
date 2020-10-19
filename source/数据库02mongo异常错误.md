# 数据库02mongo异常错误
## mongo报错WiredTiger.wt, connection: /data/db/WiredTiger.wt: handle-open: open: Operation not permitted
表现：sudo mongod可以成功启动mongo，但是不加sudo则不行，自然不希望每次都加sudo

完整报错：

```
2019-06-04T21:12:39.488+0800 I CONTROL  [initandlisten] MongoDB starting : pid=13900 port=27017 dbpath=/data/db 64-bit host=john-P95-HP
2019-06-04T21:12:39.488+0800 I CONTROL  [initandlisten] db version v3.6.3
2019-06-04T21:12:39.488+0800 I CONTROL  [initandlisten] git version: 9586e557d54ef70f9ca4b43c26892cd55257e1a5
2019-06-04T21:12:39.488+0800 I CONTROL  [initandlisten] OpenSSL version: OpenSSL 1.1.1  11 Sep 2018
2019-06-04T21:12:39.488+0800 I CONTROL  [initandlisten] allocator: tcmalloc
2019-06-04T21:12:39.488+0800 I CONTROL  [initandlisten] modules: none
2019-06-04T21:12:39.488+0800 I CONTROL  [initandlisten] build environment:
2019-06-04T21:12:39.488+0800 I CONTROL  [initandlisten]     distarch: x86_64
2019-06-04T21:12:39.488+0800 I CONTROL  [initandlisten]     target_arch: x86_64
2019-06-04T21:12:39.488+0800 I CONTROL  [initandlisten] options: {}
2019-06-04T21:12:39.495+0800 I -        [initandlisten] Detected data files in /data/db created by the 'wiredTiger' storage engine, so setting the active storage engine to 'wiredTiger'.
2019-06-04T21:12:39.495+0800 I STORAGE  [initandlisten] 
2019-06-04T21:12:39.495+0800 I STORAGE  [initandlisten] ** WARNING: Using the XFS filesystem is strongly recommended with the WiredTiger storage engine
2019-06-04T21:12:39.495+0800 I STORAGE  [initandlisten] **          See http://dochub.mongodb.org/core/prodnotes-filesystem
2019-06-04T21:12:39.495+0800 I STORAGE  [initandlisten] wiredtiger_open config: create,cache_size=7449M,session_max=20000,eviction=(threads_min=4,threads_max=4),config_base=false,statistics=(fast),log=(enabled=true,archive=true,path=journal,compressor=snappy),file_manager=(close_idle_time=100000),statistics_log=(wait=0),verbose=(recovery_progress),
2019-06-04T21:12:39.932+0800 E STORAGE  [initandlisten] WiredTiger error (1) [1559653959:932022][13900:0x7f49416a70c0], file:WiredTiger.wt, connection: /data/db/WiredTiger.wt: handle-open: open: Operation not permitted
2019-06-04T21:12:39.933+0800 E -        [initandlisten] Assertion: 28595:1: Operation not permitted src/mongo/db/storage/wiredtiger/wiredtiger_kv_engine.cpp 413
2019-06-04T21:12:39.933+0800 I STORAGE  [initandlisten] exception in initAndListen: Location28595: 1: Operation not permitted, terminating
2019-06-04T21:12:39.933+0800 I NETWORK  [initandlisten] shutdown: going to close listening sockets...
2019-06-04T21:12:39.933+0800 I NETWORK  [initandlisten] removing socket file: /tmp/mongodb-27017.sock
2019-06-04T21:12:39.933+0800 I CONTROL  [initandlisten] now exiting
```
处理：


```
# storage.dbPath
sudo chown -R mongodb:mongodb /var/lib/mongodb

# systemLog.path
sudo chown -R mongodb:mongodb /var/log/mongodb
```
这里面的mongodb:mongodb分别是用户组和用户名，

可通过如下命令查询当前用户所属组，

```
groups xxxx：xxxx是当前登录用户（一个用户可能属于多个组）  
```
参考：https://stackoverflow.com/questions/43137250/mongodb-3-4-3-permission-denied-wiredtiger-kv-engine-cpp-267-error-with-ubuntu-1

