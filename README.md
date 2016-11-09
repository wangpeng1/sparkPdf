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
4.FPGrowth 关联规则挖掘http://www.tuicool.com/articles/FNnqIrn
5. Spark 集成算法的数据格式即评估方法 以及作用解释 http://www.cnblogs.com/gnivor/p/5134632.html
6. 分类 主要是贝叶斯分类 http://blog.selfup.cn/683.html
7. 聚类算法之kmeans http://m.blog.csdn.net/article/details?id=49890667  http://my.oschina.net/xiaoluobutou/blog/680638
8. 决策树 http://www.codeweblog.com/spark-decision-tree/
有以下一些指标：精确度，召回率等。理解如下：
假设原始样本中有两类，其中：
1：总共有 P个类别为1的样本，假设类别1为正例。
2：总共有N个类别为0 的样本，假设类别0为负例。
经过分类后：
3：有 TP个类别为1 的样本被系统正确判定为类别1，FN 个类别为1 的样本被系统误判定为类别 0，
显然有P=TP+FN；
4：有 FP 个类别为0 的样本被系统误判断定为类别1，TN 个类别为0 的样本被系统正确判为类别 0，
显然有N=FP+TN；
那么：
精确度（Precision）：
P = TP/(TP+FP) ; 反映了被分类器判定的正例中真正的正例样本的比重（
准确率（Accuracy）
A = (TP + TN)/(P+N) = (TP + TN)/(TP + FN + FP + TN);
反映了分类器统对整个样本的判定能力——能将正的判定为正，负的判定为负
召回率(Recall)，也称为 True Positive Rate:
R = TP/(TP+FN) = 1 - FN/T; 反映了被正确判定的正例占总的正例的比重
9.决策树 例子http://blog.selfup.cn/877.html

10 关联规则 http://paginas.fe.up.pt/~ec/ 相关课程
   Support
Every association rule has a support and a confidence. 
“The support is the percentage of transactions that demonstrate the rule.”

Example:  Database with transactions ( customer_# : item_a1, item_a2, … )

   1:   1, 3, 5.
   2:   1, 8, 14, 17, 12.
   3:   4, 6, 8, 12, 9, 104.
   4:   2, 1, 8.

support  {8,12} = 2 (,or 50% ~ 2 of 4 customers)
support {1, 5} = 1 (,or 25% ~ 1 of 4 customers )
support {1}  = 3 (,or 75% ~ 3 of 4 customers)

Support
     An itemset is called frequent if its support is equal or greater than an agreed upon minimal value – the support threshold
	add to previous example: 
		if threshold 50%
		then itemsets {8,12} and {1} called frequent
  
  Confidence
Every association rule has a support and a confidence. 

	An association rule is of the form:    X => Y
		
X => Y: if someone buys X, he also buys Y

	The confidence is the conditional probability that, given X present in a transition , Y will also be present.

	Confidence measure, by definition: 
	Confidence(X=>Y) equals support(X,Y) / support(X) 
We should only consider rules derived from itemsets with high support, and that also have high confidence.

	“A rule with low confidence is not meaningful.”

	Rules don’t explain anything, they just point out hard facts in data volumes.
  Notice: High Confidence, Low Support.
		-> Rule ( {9}  =>  {3} ) not meaningful 

12 http://mojijs.com/2016/06/216402/index.html 
   http://stackoverflow.com/questions/33215379/precision-recall-accuracy-calculation-for-decision-tree-in-scala-spark-environ
   Accuracy(准确率), Precision(精确率)
   Confusion Matrix
    Predicted
Actual  0   1   
0   16877   251 
1   2       20  
The above is the Spark calculation of the Confusion Matrix.

The arrangement,

Predicted
Actual  0   1
    0   TN  FN
    1   FP  TP
So, Precision = TP/(TP+FP)=20/(20+2) =0.9091 Recall = TP/(TP+FN) = 20/(20+251) =0.074.

Precision(精确率), Recall(召回率)
.	实际为真	实际为假
预测为真	TP	FP
预测为假	FN	TN
# 前面的T和F，代表预测是否正确
# 后面的P和N，代表预测是真还是假
TP：预测为真，正确了
FP：预测为真，结果错了
TN：预测为假，正确了
FN：预测为假，结果错了
Precision=TPTP+FP=预测为真，实际也为真预测为真的总数
Recall=TPTP+FN=预测为真，实际也为真实际为真的总数
13 svm 参数详细说明http://www.thinkface.cn/thread-4451-1-1.html
