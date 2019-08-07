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


# A Solution

We can leverage the `__name__` attribute to create a clearer column name and maybe even one others can make sense of. 

Obviously we could rename any of these columns after the dataframe is return, but in this case I wanted a solution where I could set names on the fly. 

## Taking Advantage of the `__name__` Attribute

If you're unfamiliar, the `__name__` attribute is something every function you or someone else defines in python comes along with.

```python
def this_function():
    pass 

print(this_function.__name__)
```
> `this_function`

We can change this attribute after we define it: 
```python
def this_function():
    pass 

this_function.__name__ = 'that.'

print(this_function.__name__)
```
> `that.`

There are also some great options for adjusting function names as you define the function using decorators. More about that [here](https://stackoverflow.com/questions/10874432/possible-to-change-function-name-in-definition). 

Returning to our application, lets examine the following situation: 

```python
def my_agg(x): 
    return (x/20).sum()

iris.groupby('species').agg({
    'sepal_length': [my_agg],
    'sepal_width': [my_agg]
}).round(2)
```

<table border="1" class="dataframe">  <thead>    <tr>      <th></th>      <th>sepal_length</th>      <th>sepal_width</th>    </tr>    <tr>      <th></th>      <th>my_agg</th>      <th>my_agg</th>    </tr>    <tr>      <th>species</th>      <th></th>      <th></th>    </tr>  </thead>  <tbody>    <tr>      <th>setosa</th>      <td>12.52</td>      <td>8.57</td>    </tr>    <tr>      <th>versicolor</th>      <td>14.84</td>      <td>6.92</td>    </tr>    <tr>      <th>virginica</th>      <td>16.47</td>      <td>7.44</td>    </tr>  </tbody></table>

We could add a line adjusting the `__name__` of `my_agg()` before we start our aggregation. But what if we could rename the function as we were aggregating? Similar to how we can rename columns in a SQL statement as we define them. 

## Higher-order Renaming Function

To solve this problem, we can define a higher-order function which returns a copy of our original function, but with the name attribute changed. It looks like this: 

```python 
def renamer(agg_func,desired_name):
    def return_func(x):
        return agg_func(x)
    return_func.__name__ = desired_name
    return return_func
```
We can apply this function outside of our application of `my_agg` to reset the `__name__` on-the-fly: 

```python
df2 = iris.groupby('species').agg({
    'sepal_length': [renamer(my_agg,'Cool Name')],
    'sepal_width': [renamer(my_agg,'Better Name')]
}).round(2)
```

<table border="1" class="dataframe">  <thead>    <tr>      <th></th>      <th>sepal_length</th>      <th>sepal_width</th>    </tr>    <tr>      <th></th>      <th>Cool Name</th>      <th>Better Name</th>    </tr>    <tr>      <th>species</th>      <th></th>      <th></th>    </tr>  </thead>  <tbody>    <tr>      <th>setosa</th>      <td>12.52</td>      <td>8.57</td>    </tr>    <tr>      <th>versicolor</th>      <td>14.84</td>      <td>6.92</td>    </tr>    <tr>      <th>virginica</th>      <td>16.47</td>      <td>7.44</td>    </tr>  </tbody></table>





