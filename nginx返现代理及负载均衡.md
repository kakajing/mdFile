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