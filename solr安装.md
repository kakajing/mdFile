###solr安装

>**第一步：安装jdk、安装tomcat**

    [root@jing ~]# cp apache-tomcat-7.0.47 /usr/local/solr/tomcat -r 
>**第二步：解压solr压缩包**

	`[root@jing ~]# tar -zxf solr-4.10.3.tgz.tgz `
> **第三步：把dist/solr-4.10.3.war部署到tomcat下。**

    [root@jing dist]# cp solr-4.10.3.war /usr/local/solr/tomcat/webapps/solr.war


> **第四步：解压缩war包。启动tomcat解压。**

    [root@jing webapps]# cd /usr/local/solr/tomcat/
    [root@jing tomcat]# bin/startup.sh 
    [root@jing tomcat]# bin/shutdown.sh
    [root@jing webapps]# rm -rf solr.war
> **第五步：需要把/root/solr-4.10.3/example/lib/ext目录下的所有的jar包添加到solr工程中。**

    [root@jing ext]# cd /root/solr-4.10.3/example/lib/ext
	[root@jing ext]# cp * /usr/local/solr/tomcat/webapps/solr/WEB-INF/lib/

> **第六步：创建solrhome。把/root/solr-4.10.3/example/solr文件夹复制一份作为solrhome。**

    [root@jing example]# cp -r solr /usr/local/solr/solrhome

> **第七步：告诉solr服务solrhome的位置。需要修改web.xml**

    <env-entry>
       <env-entry-name>solr/home</env-entry-name>
       <env-entry-value>/usr/local/solr/solrhome</env-entry-value>
       <env-entry-type>java.lang.String</env-entry-type>
    </env-entry>

> **第八步：启动tomcat。**

####配置中文分析器、自定义业务域

> 第一步：把IKAnalyzer依赖的jar包添加到solr工程中。把分析器使用的扩展词典添加到classpath中。

    [root@jing IK Analyzer 2012FF_hf1]# cp IKAnalyzer2012FF_u1.jar  /usr/local/solr/tomcat/webapps/solr/WEB-INF/lib/

	[root@jing WEB-INF]# mkdir classes
	[root@jing ~]# /usr/local/solr/tomcat/webapps/solr/WEB-INF/classes
	
	[root@jing IK Analyzer 2012FF_hf1]# cp ext_stopword.dic IKAnalyzer.cfg.xml mydict.dic /usr/local/solr/tomcat/webapps/solr/WEB-INF/classes

> 第二步：需要自定义一个FieldType。Schema.xml中定义。可以在FieldType中指定中文分析器。

    [root@jing ~]# cd /usr/local/solr/solrhome/collection1/conf/
    
    [root@jing conf]# vim schema.xml
    在末尾行添加
    <fieldType name="text_ik" class="solr.TextField">
       <analyzer class="org.wltea.analyzer.lucene.IKAnalyzer"/>
    </fieldType>

	<field name="item_title" type="text_ik" indexed="true" stored="true"/>
    <field name="item_sell_point" type="text_ik" indexed="true" stored="true"/>
    <field name="item_price"  type="long" indexed="true" stored="true"/>
    <field name="item_image" type="string" indexed="false" stored="true" />
    <field name="item_category_name" type="string" indexed="true" stored="true" />
    <field name="item_desc" type="text_ik" indexed="true" stored="false" />

    <field name="item_keywords" type="text_ik" indexed="true" stored="false" multiValued="t
	rue"/>
    <copyField source="item_title" dest="item_keywords"/>
    <copyField source="item_sell_point" dest="item_keywords"/>
    <copyField source="item_category_name" dest="item_keywords"/>
    <copyField source="item_desc" dest="item_keywords"/>

> 第四步：重新启动tomcat

