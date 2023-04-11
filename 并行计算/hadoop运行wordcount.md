[hadoop集群搭建（基于docker-compose）_bde2020/hadoop-namenode_忧郁懒虫的博客-CSDN博客](https://blog.csdn.net/weixin_44608754/article/details/123274903)
[GitHub - big-data-europe/docker-hadoop: Apache Hadoop docker image](https://github.com/big-data-europe/docker-hadoop)

创建集群

[docker-compose 运行hadoop_wenwenxiong的博客-CSDN博客](https://blog.csdn.net/wenwenxiong/article/details/78973755)

[如何运行Hadoop自带的Wordcount案例 - 简书](https://www.jianshu.com/p/5b350fea3c69)

### 验证MapReduce

  

> The quick brown fox jumps over a lazy dog. It's a classic sentence often used to showcase the English language's full range of letters. With all 26 letters of the alphabet present, this sentence is a perfect example of a pangram. It's also an excellent way to practice touch typing and improve your typing speed. In addition, this sentence has become a popular phrase to use for creative writing exercises. The story might start with this sentence, or it could be incorporated in a more subtle way. Regardless of how it's used, "The quick brown fox jumps over a lazy dog" is a phrase that has stood the test of time and continues to inspire creativity in writers and typists alike.

  

创建`input.txt`， 内容如上

  

```bash

[speit@hadoop-data-2] echo "..." > input.txt

```

  

在HDFS上创建`/input`目录，并放入`input.txt`

  

```bash

[speit@hadoop-data-2] hdfs dfs -mkdir /input

[speit@hadoop-data-2] hdfs dfs -put ./input.txt /input

```

  

运行示例WordCount程序

  

```bash

[speit@hadoop-data-2] hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.10.1.jar wordcount /input /output/

```

  

查看结果

  

```bash

[speit@hadoop-data-2] hdfs dfs -ls /output

Found 2 items

-rw-r--r-- 3 speit supergroup 0 2022-03-14 20:18 /output/_SUCCESS

-rw-r--r-- 3 speit supergroup 531 2022-03-14 20:18 /output/part-r-00000

[speit@hadoop-data-2] hdfs dfs -cat /output/part-r-00000

```



运行结果：

```
root@c1e2e86131af:/# hadoop jar ./opt/hadoop-3.2.1/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.2.1.jar wordcount
/input /output
2023-04-11 11:12:07,778 INFO client.RMProxy: Connecting to ResourceManager at resourcemanager/172.18.0.4:8032
2023-04-11 11:12:07,923 INFO client.AHSProxy: Connecting to Application History server at historyserver/172.18.0.6:10200
2023-04-11 11:12:08,129 INFO mapreduce.JobResourceUploader: Disabling Erasure Coding for path: /tmp/hadoop-yarn/staging/root/.staging/job_1681210128474_0001
2023-04-11 11:12:08,222 INFO sasl.SaslDataTransferClient: SASL encryption trust check: localHostTrusted = false, remoteHostTrusted = false
2023-04-11 11:12:08,338 INFO input.FileInputFormat: Total input files to process : 1
2023-04-11 11:12:08,363 INFO sasl.SaslDataTransferClient: SASL encryption trust check: localHostTrusted = false, remoteHostTrusted = false
2023-04-11 11:12:08,780 INFO sasl.SaslDataTransferClient: SASL encryption trust check: localHostTrusted = false, remoteHostTrusted = false
2023-04-11 11:12:08,787 INFO mapreduce.JobSubmitter: number of splits:1
2023-04-11 11:12:08,887 INFO sasl.SaslDataTransferClient: SASL encryption trust check: localHostTrusted = false, remoteHostTrusted = false
2023-04-11 11:12:08,900 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1681210128474_0001
2023-04-11 11:12:08,900 INFO mapreduce.JobSubmitter: Executing with tokens: []
2023-04-11 11:12:09,038 INFO conf.Configuration: resource-types.xml not found
2023-04-11 11:12:09,039 INFO resource.ResourceUtils: Unable to find 'resource-types.xml'.
2023-04-11 11:12:09,494 INFO impl.YarnClientImpl: Submitted application application_1681210128474_0001
2023-04-11 11:12:09,523 INFO mapreduce.Job: The url to track the job: http://resourcemanager:8088/proxy/application_1681210128474_0001/
2023-04-11 11:12:09,523 INFO mapreduce.Job: Running job: job_1681210128474_0001
2023-04-11 11:12:15,611 INFO mapreduce.Job: Job job_1681210128474_0001 running in uber mode : false
2023-04-11 11:12:15,612 INFO mapreduce.Job:  map 0% reduce 0%
2023-04-11 11:12:21,681 INFO mapreduce.Job:  map 100% reduce 0%
2023-04-11 11:12:25,702 INFO mapreduce.Job:  map 100% reduce 100%
2023-04-11 11:12:25,708 INFO mapreduce.Job: Job job_1681210128474_0001 completed successfully
2023-04-11 11:12:25,793 INFO mapreduce.Job: Counters: 54
        File System Counters
                FILE: Number of bytes read=507
                FILE: Number of bytes written=459519
                FILE: Number of read operations=0
                FILE: Number of large read operations=0
                FILE: Number of write operations=0
                HDFS: Number of bytes read=781
                HDFS: Number of bytes written=702
                HDFS: Number of read operations=8
                HDFS: Number of large read operations=0
                HDFS: Number of write operations=2
                HDFS: Number of bytes read erasure-coded=0
        Job Counters
                Launched map tasks=1
                Launched reduce tasks=1
                Rack-local map tasks=1
                Total time spent by all maps in occupied slots (ms)=8324
                Total time spent by all reduces in occupied slots (ms)=14272
                Total time spent by all map tasks (ms)=2081
                Total time spent by all reduce tasks (ms)=1784
                Total vcore-milliseconds taken by all map tasks=2081
                Total vcore-milliseconds taken by all reduce tasks=1784
                Total megabyte-milliseconds taken by all map tasks=8523776
                Total megabyte-milliseconds taken by all reduce tasks=14614528
        Map-Reduce Framework
                Map input records=1
                Map output records=121
                Map output bytes=1164
                Map output materialized bytes=499
                Input split bytes=101
                Combine input records=121
                Combine output records=85
                Reduce input groups=85
                Reduce shuffle bytes=499
                Reduce input records=85
                Reduce output records=85
                Spilled Records=170
                Shuffled Maps =1
                Failed Shuffles=0
                Merged Map outputs=1
                GC time elapsed (ms)=375
                CPU time spent (ms)=4280
                Physical memory (bytes) snapshot=655396864
                Virtual memory (bytes) snapshot=13620219904
                Total committed heap usage (bytes)=568328192
                Peak Map Physical memory (bytes)=457760768
                Peak Map Virtual memory (bytes)=5137301504
                Peak Reduce Physical memory (bytes)=197636096
                Peak Reduce Virtual memory (bytes)=8482918400
        Shuffle Errors
                BAD_ID=0
                CONNECTION=0
                IO_ERROR=0
                WRONG_LENGTH=0
                WRONG_MAP=0
                WRONG_REDUCE=0
        File Input Format Counters
                Bytes Read=680
        File Output Format Counters
                Bytes Written=702
root@c1e2e86131af:/#
```

查看output

```
root@c1e2e86131af:/# hdfs dfs -ls /output
Found 2 items
-rw-r--r--   3 root supergroup          0 2023-04-11 11:12 /output/_SUCCESS
-rw-r--r--   3 root supergroup        702 2023-04-11 11:12 /output/part-r-00000
root@c1e2e86131af:/# hdfs dfs -cat /output/part-r-00000
2023-04-11 11:14:44,226 INFO sasl.SaslDataTransferClient: SASL encryption trust check: localHostTrusted = false, remoteHostTrusted = false
26      1
English 1
In      1
It's    2
Regardless      1
The     3
With    1
a       8
addition,       1
alike.  1
all     1
alphabet        1
also    1
an      1
and     3
be      1
become  1
brown   2
classic 1
continues       1
could   1
creative        1
creativity      1
dog     1
dog.    1
example 1
excellent       1
exercises.      1
for     1
fox     2
full    1
has     2
how     1
improve 1
in      2
incorporated    1
inspire 1
is      2
it      1
it's    1
jumps   2
language's      1
lazy    2
letters 1
letters.        1
might   1
more    1
of      5
often   1
or      1
over    2
pangram.        1
perfect 1
phrase  2
popular 1
practice        1
present,        1
quick   2
range   1
sentence        3
sentence,       1
showcase        1
speed.  1
start   1
stood   1
story   1
subtle  1
test    1
that    1
the     3
this    3
time    1
to      4
touch   1
typing  2
typists 1
use     1
used    1
used,   1
way     1
way.    1
with    1
writers 1
writing 1
your    1
root@c1e2e86131af:/#
```
