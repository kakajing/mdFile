#### FastDFS的搭建

* * *
- 把fastDFS上传到Linux系统，安装FastDFS之前，先安装libevent工具包
`
		yum -y install libevent
`
- 安装libfastcommonV1.0.7工具包
`
        1、解压缩
        2、./make.sh
        3、./make.sh install
        4、把/usr/lib64/libfastcommon.so文件向/usr/lib/下复制一份
        [root@jing lib64]#pwd
        /usr/lib64
        [root@jing ~]#cp libfastcommon.so ../lib

`
- 安装Tracker服务
`
		解压fastdfs-nginx-module_v1.16.tar.gz
        [root@jing ~]# tar zxf FastDFS_v5.05.tar.gz 
        //安装后在/usr/bin/目录下有以fdfs开头的文件都是编译出来的
        [root@jing FastDFS]# ./make.sh
        [root@jing FastDFS]# ./make.sh install
        //4、把/root/FastDFS/conf目录下的所有的配置文件都复制到/etc/fdfs下
        [root@jing conf]# pwd
		/root/FastDFS/conf
		[root@jing conf]# cp * /etc/fdfs/
        [root@jing conf]# ll /etc/fdfs/
        //5、配置tracker服务。修改/root/FastDFS/conf/tracker.conf文件
        [root@jing conf]# vim tracker.conf 
        	base_path=/home/fastdfs/tracker
            //6、启动tracker(启动tracker要指定/etc/fads/tracker.conf)
            [root@jing conf]# cp tracker.conf /etc/fdfs/
            [root@jing conf]# /usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf
            //重启使用命令
            [root@jing conf]# /usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf restart
`
- 安装storage服务
	1、如果是在不同的服务器安装,tracker的安装需要重新执行
    2、配置storage服务。修改/root/FastDFS/conf/storage.conf文件
    `
        base_path=/home/fastdfs/storage
        store_path0=/home/fastdfs/storage
        tracker_server=192.168.25.133:22122
    `
	3、启动storage服务
    `
    	[root@jing fdfs]# /usr/bin/fdfs_storaged /etc/fdfs/storage.conf
    `
    
######     测试服务
1、修改配置文件/etc/fdfs/client.conf
`
  	base_path=/home/fastdfs/client
    tracker_server=192.168.25.133:22122
`
2、测试
`
[root@jing fdfs]# /usr/bin/fdfs_test /etc/fdfs/client.conf upload anti-steal.jpg
`
查看图片
`
[root@jing fastdfs]# cd storage/data/00/00/
`

- 搭建nginx提供http服务。可以使用官方提供的nginx插件。要使用nginx插件需要重新编译
`
[root@jing ~]# tar zxf fastdfs-nginx-module_v1.16.tar.gz
`
2、修改/root/fastdfs-nginx-module/src/config文件，把其中的local去掉
3、对nginx重新config

```
./configure \
--prefix=/usr/local/nginx \
--pid-path=/var/run/nginx/nginx.pid \
--lock-path=/var/lock/nginx.lock \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--with-http_gzip_static_module \
--http-client-body-temp-path=/var/temp/nginx/client \
--http-proxy-temp-path=/var/temp/nginx/proxy \
--http-fastcgi-temp-path=/var/temp/nginx/fastcgi \
--http-uwsgi-temp-path=/var/temp/nginx/uwsgi \
--http-scgi-temp-path=/var/temp/nginx/scgi \
--add-module=/root/fastdfs-nginx-module/src


```

4、make
5、make install
6、把/root/fastdfs-nginx-module/src/mod_fastdfs.conf文件复制到/etc/fdfs目录下。编辑
`[root@jing src]# cp mod_fastdfs.conf /etc/fdfs/`
`[root@jing fdfs]# vim mod_fastdfs.conf `
`tracker_server=192.168.25.133:22122`
`url_have_group_name = true`
`store_path0=/home/fastdfs/storage`

7、nginx的配置，在nginx的配置文件中添加一个Server：
```
server {
        listen       80;
        server_name  192.168.101.3;

        location /group1/M00/{
                #root /home/FastDFS/fdfs_storage/data;
                ngx_fastdfs_module;
        }
}

```

8、将libfdfsclient.so拷贝至/usr/lib下
`cp /usr/lib64/libfdfsclient.so /usr/lib/`
9、启动nginx
`./nginx`

生成图片地址
`[root@jing fdfs]# /usr/bin/fdfs_test /etc/fdfs/client.conf upload anti-steal.jpg`
在浏览器上输入图片地址
`http://192.168.25.133/group1/M00/00/00/wKiagVf2ClqAY_0CAABdrZgsqUU522_big.jpg`





