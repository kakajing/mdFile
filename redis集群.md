###### redis安装
1. 安装gcc编译环境
`[root@jing ~]# yum install gcc-c++`
2. 把redis的源码上传到linux服务器
3. 解压缩
`[root@jing ~]# tar -zxvf redis-3.0.0.tar.gz`
4. `[root@jing redis-3.0.0]# make`
5. `[root@jing redis-3.0.0]# make install PREFIX=/usr/local/redis`

###### 启动redis
前端启动：

`[root@jing redis-3.0.0]# ./redis-server`
后台启动：
1、复制redis.conf到redis的安装目录

`[root@jing redis-3.0.0]# cp redis.conf /usr/local/redis/bin/`

2、修改redis.conf。修改`daemonize yes`

3、`[root@bogon redis]# ./redis-server redis.conf`

###### 客户端
`[root@jing bin]# ./redis-cli`

###### redis集群搭建
- 创建6个redis实例指定端口从7001到7006
`[root@jing local]# mkdir redis-cluster`
`[root@jing local]# cp redis redis-cluster/ -r`
`[root@jing redis-cluster]# mv redis redis01`

	删除dump.rdb文件
	`[root@jing redis01]# rm dump.rdb`
	修改redis.conf文件
	` #cluster-enabled yes`去掉#号
	`port 7001`修改端口
	复制6个redis实例
	`[root@jing redis-cluster]# cp -r redis01/ redis02`
	~
	`[root@jing redis-cluster]# cp -r redis01/ redis06`
	修改各实例的端口

- 需要一个ruby脚本。在redis源码文件夹下的src目录下。redis-trib.rb
`[root@jing redis-3.0.0]# cd src/`
`[root@jing src]# ll *.rb`
	把redis-trib.rb文件复制到到redis-cluster目录下
	`[root@jing src]# cp redis-trib.rb /usr/local/redis-cluster/`

- 执行ruby脚本之前，需要安装ruby环境。
`[root@jing ~]# yum install ruby`
`[root@jing ~]# yum install rubygems`
	安装redis-trib.rb运行依赖的ruby的包。
	`[root@jing ~]# gem install redis-3.0.0.gem`

- 启动所有的redis实例
1. 编写stall-all.sh脚本
```
cd redis01
./redis-server redis.conf
cd ..
cd redis02
./redis-server redis.conf
cd ..
cd redis03
./redis-server redis.conf
cd ..
cd redis04
./redis-server redis.conf
cd ..
cd redis05
./redis-server redis.conf
cd ..
cd redis06
./redis-server redis.conf
cd ..
```
2. 启动
`[root@jing redis-cluster]# chmod +x start-all.sh `
`[root@jing redis-cluster]# ./start-all.sh `

3. 使用redis-trib.rb创建集群
```
[root@jing redis-cluster]#  ./redis-trib.rb create --replicas 1 192.168.154.128:7001 192.168.154.128:7002 192.168.154.128:7003 192.168.154.128:7004 192.168.154.128:7005  192.168.154.128:7006
```
4. 使用客户端连接集群
`[root@jing redis-cluster]# redis01/redis-cli -p 7001 -c`
