---
title: In-line Renaming of Pandas Aggregates   
layout: "post"
description: The default column names suck. Here's a quick way to improve them. 
date: 2019-08-4
---

# The Problem 

When working with aggregating dataframes in pandas, I've found myself frustrated with how the results of aggregate columns are named. Typically, the inheret the name of the column of which you're aggregating. For example, 

```python
import pandas as pd 
import numpy as np

iris = pd.read_csv('https://raw.githubusercontent.com/mwaskom/seaborn-data/master/iris.csv')

iris.groupby('species').agg({
    'sepal_length': np.mean
}).round(2)
```

<table border="1" class="dataframe">  <thead>    <tr style="text-align: right;">      <th></th>      <th>sepal_length</th>    </tr>    <tr>      <th>species</th>      <th></th>    </tr>  </thead>  <tbody>    <tr>      <th>setosa</th>      <td>5.01</td>    </tr>    <tr>      <th>versicolor</th>      <td>5.94</td>    </tr>    <tr>      <th>virginica</th>      <td>6.59</td>    </tr>  </tbody></table>


So obviously, we as the writers of the above code know that we took a mean of sepal length. But just looking at the output we have no idea what was done to the sepal length value. We can get around this if we enclose the aggregate function in a list: 

```python
iris.groupby('species').agg({
    'sepal_length': [np.mean]
}).round(2)
```

<table border="1" class="dataframe">  <thead>    <tr>      <th></th>      <th>sepal_length</th>    </tr>    <tr>      <th></th>      <th>mean</th>    </tr>    <tr>      <th>species</th>      <th></th>    </tr>  </thead>  <tbody>    <tr>      <th>setosa</th>      <td>5.01</td>    </tr>    <tr>      <th>versicolor</th>      <td>5.94</td>    </tr>    <tr>      <th>virginica</th>      <td>6.59</td>    </tr>  </tbody></table>

Pandas adds a row to tell us the different aggregate functions we applied to the column. In this case, we only applied one, but you could see how it would work for multiple aggregate applications. 

When using this approach, a solid method I've used for creating more accessible column names is to use a concatenation approach, inpsired by [this stack overflow post](https://stackoverflow.com/questions/14507794/pandas-how-to-flatten-a-hierarchical-index-in-columns) (note that other implementations similarly use `.ravel()`): 

```python 
df = iris.groupby('species').agg({
    'sepal_length': [np.mean]
}).round(2)
df.columns = ['_'.join(gp) for gp in df.columns.values]
df
```

<table border="1" class="dataframe">  <thead>    <tr style="text-align: right;">      <th></th>      <th>sepal_length_mean</th>    </tr>    <tr>      <th>species</th>      <th></th>    </tr>  </thead>  <tbody>    <tr>      <th>setosa</th>      <td>5.01</td>    </tr>    <tr>      <th>versicolor</th>      <td>5.94</td>    </tr>    <tr>      <th>virginica</th>      <td>6.59</td>    </tr>  </tbody></table>


This approach is better than nothing but has a few issues:

- The column names still might be unclear and not work for every application; 
- These names are still far from readible english; 
- Pandas takes the `__name__` attribute of any custom functions and uses it here. If we're applying custom functions or lambda function that we're applying, they probably don't have names which make sense in this case. 





