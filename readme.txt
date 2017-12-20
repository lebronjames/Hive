Hive安装测试，与Hadoop集成

一、官网下载二进制文件
http://hive.apache.org/downloads.html

二、解压Hive，到/usr/local目录，将解压后的目录名mv为hive
设定环境变量HADOOP_HOME，HIVE_HOME，将bin目录加入到PATH中


三、修改hive配置文件
cd /usr/local/hive/conf
cp hive-default.xml.template hive-site.xml
修改hive.metastore.schema.verification，设定为false
创建/usr/local/hive/tmp目录，替换${system:java.io.tmpdir}为该目录
替换${system:user.name}为root

四、初始化derby数据库
schematool -initSchema -dbType derby
会在当前目录下简历metastore_db的数据库。
注意！！！下次执行hive时应该还在同一目录，默认到当前目录下寻找metastore。
遇到问题，把metastore_db删掉，重新执行命令
实际工作环境中，经常使用mysql作为metastore的数据

五、启动Hive（前提hdfs、yarn都已启动）
cd /usr/local/hive
hive

六、观察hadoop fs -ls /tmp/hive中目录的创建

七、在Hive中创建表
cd /usr/local/hive
hive
show databases;
use default;
create table word_count(line string);
show tables;
select * from word_count;

八、观察hadoop fs -ls /user，准备HDFS相应测试数据，上传到/wcinput
hadoop fs -ls /
hadoop fs -mkdir /wcinput/
hadoop fs -put /usr/local/hadoop/hadoop_temp/3.txt /wcinput

九、hive加载数据处理
load data inpath '/wcinput' overwrite into table word_count;

十、hive执行相应操作，查看执行结果（执行结果放在新创建表word_count_result中）
select * from word_count;
select split(line, ' ') from word_count;
select explode(split(line, ' ')) from word_count;
select word, count(1) as count from (select explode(split(line, ' ')) as word from word_count) w group by word;
select word, count(1) as count from (select explode(split(line, ' ')) as word from word_count) w group by word order by count;
create table word_count_result as select word, count(1) as count from (select explode(split(line, ' ')) as word from word_count) w group by word order by count;
select * from word_count_result;















