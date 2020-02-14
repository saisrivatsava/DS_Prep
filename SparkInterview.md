## Difference between GroupByKey and ReduceByKey

```
val words = Array("one", "two", "two", "three", "three", "three")
val wordPairsRDD = sc.parallelize(words).map(word => (word, 1))

val wordCountsWithReduce = wordPairsRDD
  .reduceByKey(_ + _)
  .collect()

val wordCountsWithGroup = wordPairsRDD
  .groupByKey()
  .map(t => (t._1, t._2.sum))
  .collect()

```

![](https://databricks.gitbooks.io/databricks-spark-knowledge-base/content/images/reduce_by.png)

![](https://databricks.gitbooks.io/databricks-spark-knowledge-base/content/images/group_by.png)

* groupByKey can cause out of disk problems as data is sent over the network and collected on the reduce workers.
* for ReduceBykey, Data are combined at each partition, only one output for one key at each partition to send over the network.
* **reduceByKey** works much better on a large dataset. That's because Spark knows it can combine output with a common key on each partition before shuffling the data.



## Repartition vs Coalesce 

### Repartition
* Return a new RDD that has exactly numPartitions partitions.
* Can increase or decrease the level of parallelism in this RDD. Internally, this uses a shuffle to redistribute data.
* If you are decreasing the number of partitions in this RDD, consider using `coalesce`, which can avoid performing a shuffle.
   
```
def repartition(numPartitions: Int)(implicit ord: Ordering[T] = null): RDD[T] = withScope {
    coalesce(numPartitions, shuffle = true)
  }
 ```


### Coalesce
* Return a new RDD that is reduced into `numPartitions` partitions.
   
* This results in a narrow dependency, e.g. if you go from 1000 partitions to 100 partitions, there will not be a shuffle, instead each of the 100
    new partitions will claim 10 of the current partitions.
   
* However, if you're doing a drastic coalesce, e.g. to numPartitions = 1, this may result in your computation taking place on fewer nodes than you like (e.g. one node in the case of numPartitions = 1). 
* To avoid this, you can pass shuffle = true. This will add a shuffle step, but means the current upstream partitions will be executed in parallel (per whatever
    the current partitioning is).
   
* Note: With shuffle = true, you can actually coalesce to a larger number of partitions.
* This is useful if you have a small number of partitions, say 100, potentially with a few partitions being abnormally large.
* Calling coalesce(1000, shuffle = true) will result in 1000 partitions with the data distributed using a hash partitioner.
