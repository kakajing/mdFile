# nginx的反向代理及负载均衡
****
#### nginx反向代理的模拟

- 安装并解压tomcat
`[root@jing ~]# tar -zxf apache-tomcat-7.0.47.tar.gz`

- 新建2个tomcat
`[root@jing ~]# mkdir /usr/local/tomcats
`
`[root@jing ~]# cp apache-tomcat-7.0.47 /usr/local/tomcats/tomcat1 -r`
`[root@jing ~]# cp apache-tomcat-7.0.47 /usr/local/tomcats/tomcat2 -r`

- 修改server.xml文件里的端口
`[root@jing tomcats]# cd tomcat2/conf/`
`[root@jing conf]# vim server.xml`

- 启动tomcat
`[root@jing tomcats]# tomcat1/bin/startup.sh`
`[root@jing tomcats]# tomcat2/bin/start.sh`

##### 反向代理
- 应该有一个nginx服务器有多个应用服务器（可以是tomcat）可以使用一台虚拟机，安装一个nginx，多个tomcat，来模拟。
```
		upstream tomcats{
            server 192.168.25.148:8081;
            server 192.168.25.148:8081;
           }

       server {
            listen       80;
            server_name  tomcat.taotao.com;

            #charset koi8-r;

            #access_log  logs/host.access.log  main;

            location / {
                proxy_pass   http://tomcats;
                index  index.html index.htm;
            }
   }


```
##### 负载均衡
- 只需要在upstream的server后面添加一个weight即可代表权重。权重越高，分配请求的数量就越多。默认权重是1
        `
        upstream tomcats{
         	server 192.168.25.148:8080 weight=2;
            server 192.168.25.148:8081;
           }
`

