---
title: Pretty Tables with pandas  
layout: "post"
description: A guide to creating presentable tables in python using pandas. 
date: 2019-05-06
---

# Motivation 

Why not?


# Distigushing Between DataFrames and Stylers 

In pandas, we can make changes to the base DataFrame object, or we can call the styler to make display changes. Styler attributes are mutually exclusive from any base DataFrame attributes. 

Let's use the iris dataset and start off with the basic display which pops out of pandas DataFrame methods. 

```python 
import pandas as pd
import numpy as np 
iris = pd.read_csv('https://raw.githubusercontent.com/mwaskom/seaborn-data/master/iris.csv')

iris.groupby('species').agg({
    'sepal_length': np.mean
}).round(2)
```

<table border="1" class="dataframe">\n  <thead>\n    <tr style="text-align: right;">\n      <th></th>\n      <th>sepal_length</th>\n    </tr>\n    <tr>\n      <th>species</th>\n      <th></th>\n    </tr>\n  </thead>\n  <tbody>\n    <tr>\n      <th>setosa</th>\n      <td>5.01</td>\n    </tr>\n    <tr>\n      <th>versicolor</th>\n      <td>5.94</td>\n    </tr>\n    <tr>\n      <th>virginica</th>\n      <td>6.59</td>\n    </tr>\n  </tbody>\n</table>

Room for improvement here. Column name has an underscore in it, everything is lowercase, and we have no sense of units. 




