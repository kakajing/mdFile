###solr和zookeeper集群

#### zookeeper集群

> **Zookeeper需要安装jdk**
第一步：把zookeeper解压

    [root@jing ~]# tar -zxf zookeeper-3.4.6.tar.gz 

    [root@jing ~]# mkdir /usr/local/solr-cloud

第三步：把zookeeper向/usr/local/solr-cloud目录下复制三份。

    [root@jing ~]# cp -r zookeeper-3.4.6 /usr/local/solr-cloud/zookeeper01 
	[root@jing ~]# cp -r zookeeper-3.4.6 /usr/local/solr-cloud/zookeeper02
	[root@jing ~]# cp -r zookeeper-3.4.6 /usr/local/solr-cloud/zookeeper03
第四步：配置zookeeper。

1、在zookeeper01目录下创建一个data文件夹。
2、在data目录下创建一个myid的文件
3、Myid的内容为1（02对应“2”，03对应“3”）

    [root@jing zookeeper01]# echo 1 >>data/myid

4、Zookeeper02、03以此类推。
5、进入conf文件，把zoo_sample.cfg文件改名为zoo.cfg

    [root@jing conf]# cp zoo_sample.cfg zoo.cfg

6、修改zoo.cfg，把dataDir=属性指定为刚创建的data文件夹。

    dataDir=/usr/local/solr-cloud/zookeeper01/data/

7、修改zoo.cfg，把clientPort指定为不冲突的端口号（01:2181、02:2182、03:2183）

    clientPort=2181

8、在zoo.cfg中末尾处添加如下内容：

    server.1=192.168.25.154:2881:3881
	server.2=192.168.25.154:2882:3882
	server.3=192.168.25.154:2883:3883

第五步：启动zookeeper。
Zookeeper的目录下有一个bin目录。使用zkServer.sh启动zookeeper服务。
启动：`./zkServer.sh start`
关闭：`./zkServer.sh stop`
查看服务状态：`./zkServer.sh status`


#####搭建solr集群

第一步：安装四个tomcat，修改其端口号不能冲突。8080~8083
第二步：向tomcat下部署solr。把单机版的solr工程复制到tomcat下即可。

    [root@jing webapps]# cp -r solr /usr/local/solr-cloud/tomcat01/webapps/

第三步：为每个solr实例创建一solrhome。

    [root@jing solr]# cp -r solrhome/ /usr/local/solr-cloud/solrhome01 

第四步：为每个solr实例关联对应的solrhome。修改web.xml

    <env-entry>
       <env-entry-name>solr/home</env-entry-name>
       <env-entry-value>/usr/local/solr-cloud/solrhome01</env-entry-value>
       <env-entry-type>java.lang.String</env-entry-type>
    </env-entry>

第五步：修改每个solrhome下的solr.xml文件。修改host、hostPort两个属性。分别是对应的ip及端口号。

     <solrcloud>
	    <str name="host">${host:192.168.25.154}</str>
	    <int name="hostPort">${jetty.port:8082}</int>
    </solrcloud>

第六步：把配置文件上传到zookeeper。需要使用
/root/solr-4.10.3/example/scripts/cloud-scripts/zkcli.sh命令上传配置文件。
把/usr/local/solr-cloud/solrhome01/collection1/conf目录上传到zookeeper。
需要zookeeper集群已经启动。

    ./zkcli.sh -zkhost 192.168.25.154:2181,192.168.25.154:2182,192.168.25.154:2183 -cmd upconfig -confdir /usr/local/solr-cloud/solrhome01/collection1/conf -confname myconf

第七步：查看是否上传成功。
使用zookeeper的zkCli.sh命令

    [root@jing bin]# pwd
		/usr/local/solr-cloud/zookeeper01/bin
	[root@jing bin]# ./zkCli.sh 

第八步：告诉solr实例zookeeper的位置。需要修改tomcat的catalina.sh添加

```
JAVA_OPTS="-DzkHost=192.168.25.154:2181,192.168.25.154:2182,192.168.25.154:2183"
```
**每个节点都需要添加.**

第九步：启动每个solr实例。

第十步：集群分片。
将集群分为两片，每片两个副本。
浏览器输入链接
```
http://192.168.25.154:8080/solr/admin/collections?action=CREATE&name=collection2&numShards=2&replicationFactor=2
```

第十一步：删除不用collection1
浏览器输入链接
```
http://192.168.25.154:8080/solr/admin/collections?action=DELETE&name=collection1
```



