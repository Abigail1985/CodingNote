MapReduce 是一个编程模型，也是一个处理超大规模数据集的算法模型实现。使用 Mapreduce 模型，用户只需实现 Map 和 Reduce 函数，就可以轻松的实现大规模并行化计算。  
  
2. Spark 与 Hadoop 都是大数据框架。 Spark 的大部分操作都是在内存中，而 Hadoop 的 MapReduce 任务会在每次操作之后都将数据写回到物理存储介质中。因此， Spark 的速度会比 Hadoop 快很多。 Spark 除了提供 Map 和 Reduce 操作之外，还支持 SQL 查询、流数据 Spark Streaming 、图处理 GraphX 以及常用的机器学习库 MLib 等。  
  
Spark 经常运行于 Hadoop 上（因为 Spark 本身并没有分布式存储系统，需要 Hadoop 的 HDFS ），可以简单理解为 Spark 可以取代 Hadoop 中的 MapReduce