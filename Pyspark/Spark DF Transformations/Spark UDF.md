Spark also allows to create user-defined functions and use them in these two types of expressions.
We are creating a Spark session and loading the data file into a Dataframe.

We have a different kind of text for `Gender` column. However, we want to standardize it to one of the following values.
- Male
- Female
- Unknown

However, we want to create a custom function to fix this problem. So, let's define the function and call it the `parse_gender` function. This function takes a string and returns a string. So, we intend to use this function where I will pass the gender column, and it should return one of the desired values, i.e., male, female, or Unknown.

```python
def parse_gender(gender):
    female_pattern = r"^f$|f.m|w.m"
    male_pattern = r"^m$|ma|m.l"
    if re.search(female_pattern, gender.lower()):
        return "Female"
    elif re.search(male_pattern, gender.lower()):
        return "Male"
    else:
        return "Unknown"
```

We can create an expression and use the `parse_gender` function. However, we have two approaches to develop the expression
- Column Object Expression
- String Expression
## UDF in Column Object Expression.

The `withColumn()` transformation allows to transform a single column without impacting other columns in the Dataframe. It takes two arguments. The first argument is the column name that we want to transform. The next argument is a column expression.

We want to use the `parse_gender()` function and supply the `Gender` column, it will fix the gender string and return a standardized gender string. However, we cannot simply use a function in a Column Object Expression, we need to register the custom function to the driver and make it a UDF.

```python
from pyspark.sql import udf
parse_gender_udf = udf(parse_gender, returnType=StringType())
```

We are going to use the `udf()` function to register it. Now, we can use the UDF function to register the Python function using the name of the local function. We can also specify the return type of your function. The default return type is `StringType`. So, the return type is optional if the function returns a String. The `udf()` function will register it and returns a reference to the registered UDF.

```python
survey_df2 = survey_df.withColumn("Gender", parse_gender_udf("Gender"))
survey_df2.show(10)
```

Creating and use a UDF is a three-step process to use a user-defined function.
- Create function.
- Register it as UDF and get the reference.
- Use the function in your expression.

Once the  function is registered in the Spark Session and the driver will serialize and send this function to the executors for the executors can smoothly run this function. This is to register the function as a Dataframe UDF and it will not register the UDF in the catalog. It will only create a UDF and serialize the function to the executors.

# UDF in String or SQL expression

We need to register the function as a SQL function, and it should go to the catalog. And that is done using `spark.udf.register()` method. The first argument is the name of the UDF, and the second argument is the signature of the function.

```python
spark.udf.register("parse_gender_udf", parse_gender, StringType())
```

So, these two registrations are different. Here the function is registered as a SQL function and will also create one entry in the catalog.

We can use spark-catalog and get a list of all functions in the catalog. Then we loop through the list. And check if the parse gender is there in the function name. If yes, we print it.

```python
[logger.info(r) for r in spark.catalog.listFunctions() if "parse_gender" in r.name]
```

```python
survey_df3 = survey_df.withColumn("Gender", expr("parse_gender_udf(Gender)"))
survey_df3.show(10)
```