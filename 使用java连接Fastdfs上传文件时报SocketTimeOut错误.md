###### 解决:使用java连接Fastdfs,上传文件时报:SocketTimeOutException的问题.
检查配置文件，发现问题：
在storage server的conf中,第23,24行是这个:
`# the storage server port  port=23000`

这毫无疑问代表了storage server自己的端口号,
而我们新装的centOS,默认开启的端口号,是很少的! 之前的80,8080,22122端口,都是自己后来手动开的.
而这个23000,很可能没有开启!!
经过确认,的确如此,开启这个端口后,eclipse中上传文件正常! junit绿条!


- 思考:
为什么前三个端口号能意识到并手动开启,而这个端口,却在最后才想到?
因为,在centOS或putty中调试时,前三个没开的话,就会立即出问题. 
但是,第四个端口,在centOS和putty中,即使没开,也不影响上传图片或通过http访问图片的URL!
导致自己很难想到这个本身就很简单的问题.

端口查看及开启方式:
1.编辑：
`[root@jing ~]# vim /etc/sysconfig/iptables`

2.手动打开指定的端口(以23000 为例),添加如下代码:
`-A INPUT -m state --state NEW -m tcp -p tcp --dport 23000 -j ACCEPT`
`-A INPUT -m state --state NEW -m tcp -p tcp --dport 22122 -j ACCEPT`

3.重启防火墙
`[root@jing ~]# service iptables restart`

