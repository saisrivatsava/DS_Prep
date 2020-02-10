## Joins


## Types of joins
* Inner joins (keep rows with keys that exist in the left and right datasets)

* Outer joins (keep rows with keys in either the left or right datasets)

* Left outer joins (keep rows with keys in the left dataset)

* Right outer joins (keep rows with keys in the right dataset)

* Left semi joins (keep the rows in the left, and only the left, dataset where the key appears in the right dataset)

* Left anti joins (keep the rows in the left, and only the left, dataset where they do not appear in the right dataset)

* Natural joins (perform a join by implicitly matching the columns between the two datasets with the same names)

* Cross (or Cartesian) joins (match every row in the left dataset with every row in the right dataset)


## examples
```
1. Condition-less inner join
left.join(right: Dataset[_]): DataFrame

2. Inner join with a single column that exists on both sides
left.join(right: Dataset[_], usingColumn: String): DataFrame

3. Inner join with columns that exist on both sides
left.join(right: Dataset[_], usingColumns: Seq[String]): DataFrame

4. Equi-join with explicit join type
left.join(right: Dataset[_], usingColumns: Seq[String], joinType: String): DataFrame

5. Inner join
left.join(right: Dataset[_], joinExprs: Column): DataFrame

6. **Join with explicit join type. Self-joins are acceptable.**
left.join(right: Dataset[_], joinExprs: Column, joinType: String): DataFrame


* JoinExpression example
val joinExpression = left.col("col1") === right.col("col")

* Join Types
- inner (default)
- inner
- left_outer
- left_outer
- left_semi
- left_anti
- cross


* example
left.join(right, joinExpression, joinType)
```


## Optimizations
* Joins can be difficult to tune since performance are bound to both the code and the Spark configuration (number of executors, memory, etc.)
* Some of the most common issues with joins are all-to-all communication between the nodes and data skewness
* We can avoid all-to-all communication using broadcasting of small tables or of medium-sized tables if we have enough memory in the cluster
* Broadcasting is not always beneficial to performance: we need to have an eye for the Spark config
* Broadcasting can make the code unstable if broadcast tables grow through time
* Skewness leads to an uneven workload on the cluster, resulting in a very small subset of tasks to take much longer than the average
* There are multiple ways to fight skewness, one is repartitioning.
* We can create our own repartitioning key, e.g. using the key salting technique


1. more info : [medium link](https://towardsdatascience.com/the-art-of-joining-in-spark-dcbd33d693c)
