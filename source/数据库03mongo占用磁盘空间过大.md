# 数据库03mongo占用磁盘空间过大
何为过大：mongodump之前2G，导入后变成15G，大约8倍.  
原因：如果mongo版本小于3，则正常，mongo请升级到3.0版本上，目前3.6.7较稳定版  
## 错误安装方式mongo2.x版
如果您安装mongodb通过如下方式  
```
sudo apt-get install mongodb
```
那么大概率是2.7版本的，这个版本就别用了，坑死人不偿命。  
通过如下方式卸载，否则卸载不干净，会影响3.x版本的安装  
```
sudo apt-get --purge remove mongodb mongodb-clients mongodb-server  
```
执行:vim /etc/mongodb.conf  
如果内容空(文件不存在）则说明卸载成功(这个2.x版本配置文件典型位置)  

## 安装mongodb3.6版
安装步骤:   
```
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2930ADAE8CAF5059EE73BB4B58712A2291FA4AD5
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.6 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.6.list
sudo apt-get update
sudo apt-get install -y mongodb-org
```
启动mongodb  
```
1、创建配置文件：
sudo vim /etc/systemd/system/mongodb.service
2.在里面追加文本
[Unit]
Description=High-performance, schema-free document-oriented database
After=network.target
[Service]
User=mongodb
ExecStart=/usr/bin/mongod --quiet --config /etc/mongod.conf
[Install]
WantedBy=multi-user.target
3.启动服务，查看服务状态
sudo systemctl start mongodb

或常规启动方式:nohup mongod --bind_ip_all --config /etc/mongod.conf &
```