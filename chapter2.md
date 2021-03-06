---
title: 'Vectorizing the feature data'
description: 'What is Extract, Transform, and Select (ETS).  What is the CountVectorizer model.  Fitting the CountVectorizer model.  Analyzing a vectorizer''s vocabulary.  Dataset subset selection.'
---

## Vectorizer concepts

```yaml
type: VideoExercise
key: c35706e341
xp: 50
```

`@projector_key`
b6c07e4a9ea7b28692c9323b386e3161

---

## Practice vectorizer concepts

```yaml
type: DragAndDropExercise
key: 284abafefe
kind: Classify
xp: 100
```

<!-- Guidelines for contexts: https://instructor-support.datacamp.com/en/articles/2375526-course-coding-exercises. -->

`@instructions`
<!-- Guidelines for instructions https://instructor-support.datacamp.com/en/articles/2375526-course-coding-exercises. -->
- Instruction 1
- Instruction 2

`@hint`
<!-- Examples of good hints: https://instructor-support.datacamp.com/en/articles/2379164-hints-best-practices. -->
- This is an example hint.
- This is an example hint.

`@solution`
```{python}
# Edit or remove this code to create your own exercise.
# This is 1 type of drag and drop exercise, there are 2 other types. See documentation:
# http://instructor-support.datacamp.com/en/articles/3039539-course-drag-drop-exercises

# Make sure you only use SPACES, NOT TABS in front of each line.

# Drag zone that holds all the options.
# Specify an ID for this zone to use in SCTs.
- id: options
  title: "Options" # Title of your zone This is not shown with more than 2 zones.

# You can keep adding drop zones to sort to.
# This example has 2 zones.
- id: dropzone_r
  title: "R"
  items: # Each drop zone has a list of items it contains. These will be shown in a random fashion.
    - content: "stringr" # Name of an item. Feel free to use markdown.
      id: stringr # ID of the item. This can be used in the SCTs.
    - content: "dplyr"
      id: dplyr

- id: dropzone_python
  title: "Python"
  items:
    - content: "pandas"
      id: pandas
    - content: "numpy"
      id: numpy
    
```

`@sct`
```{python}
checks: # Individual checks and custom messages per item. This is optional. Without it, it will check that the options are as in the solution code.
  - condition: check_target(pandas) == dropzone_python # Check that pandas is in dropzone_python.
    incorrectMessage: 'Hmm! Pandas is a Python package.' # If that condition is not true, show this message.
  - condition: check_target(numpy) == dropzone_python
    incorrectMessage: 'Damn, this is far from perfect!'
  - condition: check_target(dplyr) == dropzone_r
    incorrectMessage: "Hmm, keep doing R courses! :-)"
  - condition: check_target(stringr) == dropzone_r
    incorrectMessage: "How funny if stringr would be a Python package."
successMessage: "Congratulations" # Message shown when all is correct.
failureMessage: "Try again!" # Message shown when there are errors (and there is no specific error available).
isOrdered: false # Should the items in the zones be ordered as in the solution code?
```

---

## Fitting a CountVectorizer model

```yaml
type: VideoExercise
key: f3e9601738
xp: 50
```

`@projector_key`
178850f353d7b2947d458bc3fe6eedb2

---

## Configuring a `CountVectorizer` object

```yaml
type: NormalExercise
key: 129afc16fd
xp: 100
```

To properly configure an instance of a `CountVectorizer` object, we need to tell it where to expect its input data to be, and where to put the data that it will generate.

`@instructions`
<!-- Guidelines for instructions https://instructor-support.datacamp.com/en/articles/2375526-course-coding-exercises. -->
- Import the `CountVectorizer` module
- Create an instance of `CountVectorizer` expecting its input to be in column called `likes`, and putting its output into a column called `likesvec`

`@hint`
- The relevant arguments here are `inputCol` and `outputCol`.

`@pre_exercise_code`
```{python}
from pyspark.sql.types import ArrayType, StringType, IntegerType, StructType, StructField
from pyspark.ml.classification import LogisticRegression
import pyspark.sql.functions as fun
from pyspark import SQLContext

sqlContext = SQLContext.getOrCreate(spark.sparkContext)

schema = StructType([StructField("uid", StringType()),
                     StructField("rabbit", IntegerType()),
                     StructField("likes", StringType())])

null_array_udf = fun.udf(lambda x:
                x if (x and type(x) is list and len(x)>0 )
                else [],
                ArrayType(StringType()))

df = spark.read.csv('data/rabbitduck/rabbitduck.csv', header=True, schema=schema)\
           .withColumn('likes', fun.split('likes', ','))\
           .withColumn('numlikes', fun.when(fun.col('likes').isNull(),0).otherwise(fun.size('likes')))\
           .withColumn('likes', null_array_udf('likes'))


# Much of the above can be removed by loading df from saved file
```

`@sample_code`
```{python}
# Import the CountVectorizer module
from pyspark.ml.feature import ____

# Get the input from likes and put the output into likesvec
cv = CountVectorizer(____='likes', ____='likesvec')

```

`@solution`
```{python}
# Import the CountVectorizer module
from pyspark.ml.feature import CountVectorizer

# Get the input from likes and put the output into likesvec
cv = CountVectorizer(inputCol='likes', outputCol='likesvec')

```

`@sct`
```{python}
success_msg("Perfect. The CountVectorizer now knows where to find its input data, and where to put its output result.")
```

---

## Practice fitting a CountVectorizer model

```yaml
type: NormalExercise
key: 46f8917ace
xp: 100
```

An instance of a `CountVectorizer` object is provided in the variable `cv`.  It expects the input column to be provided by a column `likes` and will put its output in a column called `likesvec`.

`@instructions`
- Create a fitted `CountVectorizer` model by fitting an instance of a `CountVectorizer` object upon the data provided in the dataframe `df`

`@hint`
- Apply the `fit` function to the `cv` object. Provide the name of the data dataframe as the argument.

`@pre_exercise_code`
```{python}
from pyspark.ml.feature import CountVectorizer
from pyspark.sql.types import ArrayType, StringType, IntegerType, StructType, StructField
from pyspark.ml.classification import LogisticRegression
import pyspark.sql.functions as fun
from pyspark import SQLContext

sqlContext = SQLContext.getOrCreate(spark.sparkContext)

schema = StructType([StructField("uid", StringType()),
                     StructField("rabbit", IntegerType()),
                     StructField("likes", StringType())])

null_array_udf = fun.udf(lambda x:
                x if (x and type(x) is list and len(x)>0 )
                else [],
                ArrayType(StringType()))

df = spark.read.csv('data/rabbitduck/rabbitduck.csv', header=True, schema=schema)\
           .withColumn('likes', fun.split('likes', ','))\
           .withColumn('numlikes', fun.when(fun.col('likes').isNull(),0).otherwise(fun.size('likes')))\
           .withColumn('likes', null_array_udf('likes'))

# Much of the above can be removed by loading a saved df from file

cv = CountVectorizer(inputCol='likes', outputCol='likesvec')



```

`@sample_code`
```{python}
# Fit cv to df
cv_model = ____.____(____)
```

`@solution`
```{python}
# Fit cv to df
cv_model = cv.fit(df)
```

`@sct`
```{python}
success_msg("Good. The vectorizer model now knows the vocabulary contained in df.")
```

---

## Transforming data

```yaml
type: VideoExercise
key: 523fdd1ddf
xp: 50
```

`@projector_key`
6799b93e2a7f4aa5d388a3370af77f2f

---

## Practice transforming data

```yaml
type: NormalExercise
key: 7e929385b9
xp: 100
```

The `CountVectorizer` model transforms data to a vectorized format that can be provided as the input vector to a machine learning model. We also need to rename a couple of the key columns using the names that are expected by the machine learning model.

A dataframe `df` is provided; it contains data that needs to be transformed into a form suitable for training.  A fitted CountVectorizer model `cv_model` is provided.

`@instructions`
- Transform the data in `df` using the CountVectorizer model `cv_model`

`@hint`
- Use the `transform` function on `cv_model`

`@pre_exercise_code`
```{python}
from pyspark.ml.feature import CountVectorizer
from pyspark.sql.types import ArrayType, StringType, IntegerType, StructType, StructField
from pyspark.ml.classification import LogisticRegression
import pyspark.sql.functions as fun
from pyspark import SQLContext

sqlContext = SQLContext.getOrCreate(spark.sparkContext)

schema = StructType([StructField("uid", StringType()),
                     StructField("rabbit", IntegerType()),
                     StructField("likes", StringType())])

null_array_udf = fun.udf(lambda x:
                x if (x and type(x) is list and len(x)>0 )
                else [],
                ArrayType(StringType()))

df = spark.read.csv('data/rabbitduck/rabbitduck.csv', header=True, schema=schema)\
           .withColumn('likes', fun.split('likes', ','))\
           .withColumn('numlikes', fun.when(fun.col('likes').isNull(),0).otherwise(fun.size('likes')))\
           .withColumn('likes', null_array_udf('likes'))

# Can remove much of the above by loading df from file

cv = CountVectorizer(inputCol='likes', outputCol='likesvec')
cv_model = cv.fit(df)
```

`@sample_code`
```{python}
# Use cv_model to transform the data in df
df_t = ____.____(df)
```

`@solution`
```{python}
# Use cv_model to transform the data in df
df_t = cv_model.transform(df)
```

`@sct`
```{python}
success_msg("Good. You have vectorized the feature data and renamed its columns to what is needed for the training step.")
```

---

## Labeling feature data

```yaml
type: VideoExercise
key: 65d0822c3d
xp: 50
```

`@projector_key`
35563699a1babe3c3fd3e4a2b9b0c0ac

---

## Practice labeling feature data

```yaml
type: NormalExercise
key: 7fded5cc0d
xp: 100
```

Previously we used a `CountVectorizer` model to transform data to a vectorized format that can be provided as the input vector to a machine learning model. Although the data is now in a suitable format, we also need to rename two of its columns to the column names that are expected by the machine learning model.

A dataframe `df_t` is provided that contains data that has been transformed using a CountVectorizer model.

`@instructions`
- From `df_t` select the columns `uid`, `rabbit`, `likesvec`, and `numlikes`
- Rename the `likesvec` column to `features`
- Rename the `rabbit` column to `label`

`@hint`
- Use the `select` function to select columns.
- Use the `withColumnRenamed` function to rename a column.
- Provide the previous column name as the first argument and the new name as the second argument.

`@pre_exercise_code`
```{python}
from pyspark.ml.feature import CountVectorizer
from pyspark.sql.types import ArrayType, StringType, IntegerType, StructType, StructField
from pyspark.ml.classification import LogisticRegression
import pyspark.sql.functions as fun
from pyspark import SQLContext

sqlContext = SQLContext.getOrCreate(spark.sparkContext)

schema = StructType([StructField("uid", StringType()),
                     StructField("rabbit", IntegerType()),
                     StructField("likes", StringType())])

null_array_udf = fun.udf(lambda x:
                x if (x and type(x) is list and len(x)>0 )
                else [],
                ArrayType(StringType()))

df = spark.read.csv('data/rabbitduck/rabbitduck.csv', header=True, schema=schema)\
           .withColumn('likes', fun.split('likes', ','))\
           .withColumn('numlikes', fun.when(fun.col('likes').isNull(),0).otherwise(fun.size('likes')))\
           .withColumn('likes', null_array_udf('likes'))

# Can remove much of the above by loading df from file

cv = CountVectorizer(inputCol='likes', outputCol='likesvec')
cv_model = cv.fit(df)
df_t = cv_model.transform(df)

```

`@sample_code`
```{python}
# Select uid, rabbit, likesvec, and numlikes columns
df_s = df_t.____(____, ____, ____, ____)

# Rename the likesvec column to features
df_f = df_s.____(____, ____)

# Rename the rabbit column to label
df_labeled = df_f.____(____, ____)

```

`@solution`
```{python}
# Select uid, rabbit, likesvec, and numlikes columns
df_s = df_t.select('uid', 'rabbit', 'likesvec', 'numlikes')

# Rename the likesvec column to features
df_f = df_s.withColumnRenamed('likesvec', 'features')

# Rename the rabbit column to label
df_labeled = df_f.withColumnRenamed('rabbit', 'label')

```

`@sct`
```{python}
# SCT : 
# 1. inspect df_s.columns
# 2. inspect df_f.columns, and datatype of df.features column
# 3. inspect df_labeled columns, and datatype of all columns
success_msg("Good job. The machine learning model expects the input data and label data to be in columns that it recognizes.")
```
