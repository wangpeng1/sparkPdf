# sparkPdf
This is  some spark knowledge
https://docs.cloud.databricks.com/docs/spark/1.6/index.html#examples/Dataset%20Wordcount.html

Partition Discovery 优化spark性能

mapWithState 参考 https://docs.cloud.databricks.com/docs/spark/1.6/examples/Streaming%20mapWithState.html
http://dataunion.org/21826.html

各种流的形式
http://m.blog.csdn.net/article/details?id=51836036


https://github.com/apache/spark/blob/master/examples/src/main/scala/org/apache/spark/examples/SparkTC.scala  解释
http://blog.csdn.net/li385805776/article/details/20069879



mllib中的Vector，分为DenseVector和SparseVector，DenseVector就是扎扎实实的顺序存储向量中的每一个元素，SparseVector略有不同，成员size是真正的向量长度，indices是记录所有值不为0的元素的索引，values是记录所有不为0的元素的值，比如一个向量为（0,1,0,0,2,0,0），那么indices则为数组（1,4），values则为数组（1,2），这两个数组必须长度相同。

http://blog.csdn.net/u011239443/article/details/51728659


//创建稠密向量<1.0,2.0,3.0>;Vectors.dense接收一串值或一个数组
val denseVec1 = Vectors.dense(1.0,2.0,3.0))
val denseVec2 = Vectors.dense(Array(1.0,2.0,3.0))

//创建稀疏向量<1.0,0.0,2.0,0.0>
向量的维度（4） 以及非零位的位置和对应的值
val sparseVec1 = Vectors.sparse(4,Array(0,2),Array(1.0,2.0))
