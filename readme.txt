Hive��װ���ԣ���Hadoop����

һ���������ض������ļ�
http://hive.apache.org/downloads.html

������ѹHive����/usr/localĿ¼������ѹ���Ŀ¼��mvΪhive
�趨��������HADOOP_HOME��HIVE_HOME����binĿ¼���뵽PATH��


�����޸�hive�����ļ�
cd /usr/local/hive/conf
cp hive-default.xml.template hive-site.xml
�޸�hive.metastore.schema.verification���趨Ϊfalse
����/usr/local/hive/tmpĿ¼���滻${system:java.io.tmpdir}Ϊ��Ŀ¼
�滻${system:user.name}Ϊroot

�ġ���ʼ��derby���ݿ�
schematool -initSchema -dbType derby
���ڵ�ǰĿ¼�¼���metastore_db�����ݿ⡣
ע�⣡�����´�ִ��hiveʱӦ�û���ͬһĿ¼��Ĭ�ϵ���ǰĿ¼��Ѱ��metastore��
�������⣬��metastore_dbɾ��������ִ������
ʵ�ʹ��������У�����ʹ��mysql��Ϊmetastore������

�塢����Hive��ǰ��hdfs��yarn����������
cd /usr/local/hive
hive

�����۲�hadoop fs -ls /tmp/hive��Ŀ¼�Ĵ���

�ߡ���Hive�д�����
cd /usr/local/hive
hive
show databases;
use default;
create table word_count(line string);
show tables;
select * from word_count;

�ˡ��۲�hadoop fs -ls /user��׼��HDFS��Ӧ�������ݣ��ϴ���/wcinput
hadoop fs -ls /
hadoop fs -mkdir /wcinput/
hadoop fs -put /usr/local/hadoop/hadoop_temp/3.txt /wcinput

�š�hive�������ݴ���
load data inpath '/wcinput' overwrite into table word_count;

ʮ��hiveִ����Ӧ�������鿴ִ�н����ִ�н�������´�����word_count_result�У�
select * from word_count;
select split(line, ' ') from word_count;
select explode(split(line, ' ')) from word_count;
select word, count(1) as count from (select explode(split(line, ' ')) as word from word_count) w group by word;
select word, count(1) as count from (select explode(split(line, ' ')) as word from word_count) w group by word order by count;
create table word_count_result as select word, count(1) as count from (select explode(split(line, ' ')) as word from word_count) w group by word order by count;
select * from word_count_result;















