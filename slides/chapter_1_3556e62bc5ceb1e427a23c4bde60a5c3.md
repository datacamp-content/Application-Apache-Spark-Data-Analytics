---
title: 'Insert title here'
key: 3556e62bc5ceb1e427a23c4bde60a5c3
---

## Dot notation and SQL

```yaml
type: TitleSlide
key: 25e2f4d95a
```

`@lower_third`
name: Mark Plutowski
title: Algorithmic Econometrician

`@script`
Hello and welcome to this lesson about Spark SQL. You will learn how to query using either SQL queries or using dataframe dot notation.

---

## Our table has 3 columns

```yaml
type: FullCodeSlide
key: 26bdf4d6ce
```

`@part1`
```
>>> df
DataFrame[train_id: string, station: string, time: string]
```

```
>>> df.columns
['train_id', 'station', 'time']
```


```
>>> df.show(5)
+--------+-------------+-----+
|train_id|      station| time|
+--------+-------------+-----+
|     324|San Francisco|7:59a|
|     324|  22nd Street|8:03a|
|     324|     Millbrae|8:16a|
|     324|    Hillsdale|8:24a|
|     324| Redwood City|8:31a|
+--------+-------------+-----+
```

`@script`
For example, suppose you have a dataframe containing three columns. and you want to select only two columns...

---

## We only need 2

```yaml
type: FullCodeSlide
key: 2e5dd9344c
```

`@part1`
```
df.select('train_id','station')
  .show(5)

+--------+-------------+
|train_id|      station|
+--------+-------------+
|     324|San Francisco|
|     324|  22nd Street|
|     324|     Millbrae|
|     324|    Hillsdale|
|     324| Redwood City|
+--------+-------------+
```

`@script`
You could do this.

---

## 3 ways to select 2 columns

```yaml
type: FullCodeSlide
key: f7546cbde8
```

`@part1`
- df.select('**train_id**','station')
- df.select(**df.train_id**,df.station) {{1}}
- from pyspark.sql.functions import **col** {{2}} 
- df.select(**col**('train_id'), col('station')) {{3}}

`@script`
Here's how we did that. See that the column ‘train_id’ is a string given in quotes. ... You can also do the following {{1}}, using dot notation.  This time the column is given as df.train_id. ...  You can also import this function {{2}}, which allows you to do the following {{3}}.  This time, the name of the column is given as an argument to this new operator. It may seem more verbose in this case. However, it is useful in other cases. Such as in the following. ...

---

## 2 ways to rename a column

```yaml
type: FullCodeSlide
key: 7a4f94ad95
```

`@part1`
```
df.select('train_id','station')
  .withColumnRenamed('train_id','train')
  .show(5)
```

```
+-----+-------------+
|train|      station|
+-----+-------------+
|  324|San Francisco|
|  324|  22nd Street|
|  324|     Millbrae|
|  324|    Hillsdale|
|  324| Redwood City|
+-----+-------------+
```

```
df.select(col('train_id').alias('train'), 'station')
```
{{1}}

`@script`
To rename a column you can use the withColumnRenamed function.  But you could also use the column operator, like so {{1}}.

---

## Don’t do this!

```yaml
type: FullSlide
key: 99484fa722
center_content: true
```

`@part1`
df.select('**train_id**',  **df.**station,  **col**('time'))**

`@script`
Pro tip: try not to use all three conventions at the same time without good reason.

---

## SQL queries using dot notation

```yaml
type: FullCodeSlide
key: 605f38f64b
```

`@part1`
```
spark.sql('select train_id as train, station from df limit 5')
     .show()
```

```
+-----+-------------+
|train|      station|
+-----+-------------+
|  324|San Francisco|
|  324|  22nd Street|
|  324|     Millbrae|
|  324|    Hillsdale|
|  324| Redwood City|
+-----+-------------+
```

```
df.select(col('train_id').alias('train'), 'station')
  .limit(5)
  .show()
```
{{1}}

`@script`
Most Spark sql queries can be done in either dot notation or sql notation.  Here’s an example.  Notice that the limit operation is done at query time instead of at show time. ... We can get the same result using dot notation, like so {{1}}. Note how we used the column operator to select the train_id column, and renamed it in place.

---

## Window function SQL

```yaml
type: FullCodeSlide
key: 89001687d4
```

`@part1`
```
query = """
select *, 
row_number() over(partition by train_id order by time) as id 
from df
"""
```

```
spark.sql(query)
     .show(11)
+--------+-------------+-----+---+
|train_id|      station| time| id|
+--------+-------------+-----+---+
|     217|       Gilroy|6:06a|  1|
|     217|   San Martin|6:15a|  2|
|     217|  Morgan Hill|6:21a|  3|
|     217| Blossom Hill|6:36a|  4|
|     217|      Capitol|6:42a|  5|
|     217|       Tamien|6:50a|  6|
|     217|     San Jose|6:59a|  7|
|     324|San Francisco|7:59a|  1|
|     324|  22nd Street|8:03a|  2|
|     324|     Millbrae|8:16a|  3|
|     324|    Hillsdale|8:24a|  4|
+--------+-------------+-----+---+
```

`@script`
Window functions can also be done in either sql or dot notation.  This query adds a number to each stop on a train line ... in a new column called id. Note how the id column starts over for train_id 324.

---

## Window function using dot notation

```yaml
type: FullSlide
key: 7aec9a66f7
```

`@part1`
```
from pyspark.sql import Window, 
from pyspark.sql.functions import row_number
df.withColumn("id", row_number()
                    .over(
                           Window.partitionBy('train_id')
                                 .orderBy('time')
                         )
  )
  .show()
```

- ROW_NUMBER in SQL :  pyspark.sql.functions.row_number {{1}} 
- The inside of the OVER clause : pyspark.sql.Window {{2}}
- PARTITION BY : pyspark.sql.Window.partitionBy {{3}}
- ORDER BY : pyspark.sql.Window.orderBy {{4}}

`@script`
Here’s the same result using dot notation. There is typically a dot notation equivalent of every sql clause including window functions. {{1}} the row_number sql clause has an equivalent dot notation sql function. ... {{2}} the inside of an OVER clause is handled by a Window object. ... {{3}} a Window object provides methods for it to be partitioned {{4}} ... and ordered. ... Some people prefer the SQL version, other people prefer the dot notation. 

(4:21)

---

## Window versus self-join

```yaml
type: TwoColumns
key: ca56ea28e5
```

`@part1`
Getting time of next stop 

```
+--------+-------------+-----+-----+
|train_id|      station| time| next|
+--------+-------------+-----+-----+
|     217|       Gilroy|6:06a|6:15a|
|     217|   San Martin|6:15a|6:21a|
|     217|  Morgan Hill|6:21a|6:36a|
|     217| Blossom Hill|6:36a|6:42a|
|     217|      Capitol|6:42a|6:50a|
|     217|       Tamien|6:50a|6:59a|
|     217|     San Jose|6:59a| null|
|     324|San Francisco|7:59a|8:03a|
|     324|  22nd Street|8:03a|8:16a|
|     324|     Millbrae|8:16a|8:24a|
|     324|    Hillsdale|8:24a|8:31a|
|     324| Redwood City|8:31a|8:37a|
|     324|    Palo Alto|8:37a|9:05a|
|     324|     San Jose|9:05a| null|
+--------+-------------+-----+-----+
```

`@part2`
Using self-join:
```
select a.*, min(b.time) as next 
from df as a join df as b 
on a.train_id=b.train_id 
   and a.time < b.time 
group by a.train_id, a.station, a.time 
order by train_id, time
```

Window function equivalent:
```
select *, lead(time,1) 
  over (partition by train_id 
        order by time) as next 
from df
```

`@script`
Previously we saw how to include the time from the next row on the current row using a window function.  Could we do this with regular sql? ... The answer is Yes. But it is a longer query. {{1}}  This query joins the table with itself using an inequality join.  Then, because we only want the very next value, we used the trick of using a GROUP BY, and then getting the MIN of the time column.  We can use that approach here because the time column is sorted.  What if the desired column were not sorted? ... Compare this with using the window function version {{2}}.  I find that this version is easier to understand. On realistic datasets it would also tend to run faster.

---

## Let's practice

```yaml
type: FinalSlide
key: 57db0d72de
```

`@script`
Let's try out what we've just learned. 

(4:21)
