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

<style  type="text/css" >\n</style>  \n<table id="T_47debf42_74d2_11e9_b72b_f0796010687e" > \n<thead>    <tr> \n        <th class="blank level0" ></th> \n        <th class="col_heading level0 col0" >sepal_length</th> \n    </tr>    <tr> \n        <th class="index_name level0" >species</th> \n        <th class="blank" ></th> \n    </tr></thead> \n<tbody>    <tr> \n        <th id="T_47debf42_74d2_11e9_b72b_f0796010687elevel0_row0" class="row_heading level0 row0" >setosa</th> \n        <td id="T_47debf42_74d2_11e9_b72b_f0796010687erow0_col0" class="data row0 col0" >5.01</td> \n    </tr>    <tr> \n        <th id="T_47debf42_74d2_11e9_b72b_f0796010687elevel0_row1" class="row_heading level0 row1" >versicolor</th> \n        <td id="T_47debf42_74d2_11e9_b72b_f0796010687erow1_col0" class="data row1 col0" >5.94</td> \n    </tr>    <tr> \n        <th id="T_47debf42_74d2_11e9_b72b_f0796010687elevel0_row2" class="row_heading level0 row2" >virginica</th> \n        <td id="T_47debf42_74d2_11e9_b72b_f0796010687erow2_col0" class="data row2 col0" >6.59</td> \n    </tr></tbody> \n</table>

Room for improvement here. Column name has an underscore in it, everything is lowercase, and we have no sense of units. 




