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


协同过滤是一种根据用户对各种产品的交互与评分来推荐新产品的推荐系统技术。
协同过滤引入的地方就在于它只需要输入一系列用户/产品的交互记录；
无论是显式的交互(例如在购物网站上进行评分)还是隐式的(例如用户访问了一个
产品的页面但是没有对产品评分)交互皆可。仅仅根据这些交互，协同过滤算法就能
够知道哪些产品之间比较相似(因为相同的用户与它们发生了交互)以及哪些用户之间
比较相似，然后就可以做出新的推荐。
尽管MLlib的API使用了用户和产品概念，但是也可以将协同过滤用于其他应用场景中，
比如在社交网络中推荐用户，为文章推荐要添加的标签，为电台推荐歌曲等。
 
交替最小二乘法
MLlib中包含交替最小二乘法(ALS)的一个实现，这是一个协同过滤的常用算法，可以很好的
扩展到集群上。它位于mllib.recommendation.ALS类中。
ALS会为每个用户和产品都设一个特征向量，这样用户向量与产品向量的点积就接近于它们的
得分。它接收下面所列几个参数：
  rank
        使用的特征向量的大小，更大的特征向量会产生更好的模型，但是也需要话费更大的计算代价，默认10
  iterations
        要执行的迭代次数，默认10
   lamda
        正则化参数，默认0.01
   alpha
        用来在ALS中计算置信度的常量，默认1.0
   numUserBlocks,  numProductBlocks
        切分用户和产品数据的块的数目，用来控制并行度，可以选择传递-1来上MLlib自动决定.
 
要使用ALS算法，需要有一个由mllib.recommendation.Rating对象组成的RDD，其中每个包含
一个用户id，一个产品id和一个评分。实现过程中的一个挑战是每个id都需要是一个32位的整数值。
如果id是字符串或者更大的数字，那么可以直接在ALS中使用id的哈希值，
即使有两个用户或者产品映射到同一个Id上，总体结果依然会不错。还有一种办法是broadcast()一张
从产品id到正兴致的表，来付给每个产品独特的id。

http://blog.javachen.com/2015/04/17/spark-mllib-collaborative-filtering.html 协同过滤介绍了基本的概念和多个参数方法的说明

使用Spark MLlib的ALS算法进行协同过滤，首先需要了解推荐的过程，然后需要根据测试不断修改训练测试，建立合理的模型，最后再给用户进行推荐商品，保存推荐结果。

另外，在网上找到一些Spark做推荐的项目：

提供Restfull接口的实时推荐：https://github.com/OndraFiedler/spark-recommender
spark-elasticsearch-mllib：https://github.com/ebiznext/spark-elasticsearch-mllib
Beyond Piwik Web Analytics：https://github.com/skrusche63/spark-piwik
Serves predictions via a REST API：https://github.com/SeldonIO/seldon-server
https://github.com/zhuixun/learningspark
Spark-Movie-Recommendation：https://github.com/yuriy-voderatskiy/Spark-Movie-Recommendation

2. http://blog.javachen.com/2015/06/01/how-to-implement-collaborative-filtering-using-spark-als.html   ALS实现协同过滤 有详细的说明
3. 配置参数 http://blog.javachen.com/2015/06/07/spark-configuration.html
