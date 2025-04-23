## 分析过程
### 一、数据清洗
#### 重复值、异常值、格式调整
#### 异常值处理
### 数据规整
#### 比如增加一列辅助列 月份


```python
import os
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
plt.rcParams['font.sans-serif']='SimHei'## 设置中文显示
```


```python
data = pd.read_csv('data_wuliu.csv',encoding='gbk')
data.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 1161 entries, 0 to 1160
    Data columns (total 10 columns):
     #   Column  Non-Null Count  Dtype  
    ---  ------  --------------  -----  
     0   订单号     1159 non-null   object 
     1   订单行     1161 non-null   int64  
     2   销售时间    1161 non-null   object 
     3   交货时间    1161 non-null   object 
     4   货品交货状况  1159 non-null   object 
     5   货品      1161 non-null   object 
     6   货品用户反馈  1161 non-null   object 
     7   销售区域    1161 non-null   object 
     8   数量      1157 non-null   float64
     9   销售金额    1161 non-null   object 
    dtypes: float64(1), int64(1), object(8)
    memory usage: 90.8+ KB


##### 通过info()可以看出，该数据集有10列，1161行数据，包括名字，数据量，格式等，可以得出：
##### 1.订单号，货品交货情况，数量三列数据:存在缺失值，但是缺失量不大，可以删除
##### 2.订单行，对分析无关紧要，可以考虑删除
##### 3.销售金额数据类型为 object ，数据类型不对 (万元|元，逗号问题),数据类型需要转换成int|float


```python
# 删除重复值,保留第一条数据
data.drop_duplicates(keep='first',inplace=True)
```


```python
data.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Index: 1152 entries, 0 to 1160
    Data columns (total 10 columns):
     #   Column  Non-Null Count  Dtype  
    ---  ------  --------------  -----  
     0   订单号     1150 non-null   object 
     1   订单行     1152 non-null   int64  
     2   销售时间    1152 non-null   object 
     3   交货时间    1152 non-null   object 
     4   货品交货状况  1150 non-null   object 
     5   货品      1152 non-null   object 
     6   货品用户反馈  1152 non-null   object 
     7   销售区域    1152 non-null   object 
     8   数量      1150 non-null   float64
     9   销售金额    1152 non-null   object 
    dtypes: float64(1), int64(1), object(8)
    memory usage: 99.0+ KB


#### 通过info可以看出该数据集有9条重复数据现已删除


```python
# 删除缺失值(na,删除带有na的整行数据,按照行删除，how=any是默认值)
data.dropna(axis=0,how='any',inplace=True)
data.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Index: 1146 entries, 0 to 1160
    Data columns (total 10 columns):
     #   Column  Non-Null Count  Dtype  
    ---  ------  --------------  -----  
     0   订单号     1146 non-null   object 
     1   订单行     1146 non-null   int64  
     2   销售时间    1146 non-null   object 
     3   交货时间    1146 non-null   object 
     4   货品交货状况  1146 non-null   object 
     5   货品      1146 non-null   object 
     6   货品用户反馈  1146 non-null   object 
     7   销售区域    1146 non-null   object 
     8   数量      1146 non-null   float64
     9   销售金额    1146 non-null   object 
    dtypes: float64(1), int64(1), object(8)
    memory usage: 98.5+ KB


#### 通过info可以看出该数据集有6条缺失数据现已删除


```python
# c删除对分析无意义的列订单行
data.drop(columns='订单行',inplace=True,axis=1)
data.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Index: 1146 entries, 0 to 1160
    Data columns (total 9 columns):
     #   Column  Non-Null Count  Dtype  
    ---  ------  --------------  -----  
     0   订单号     1146 non-null   object 
     1   销售时间    1146 non-null   object 
     2   交货时间    1146 non-null   object 
     3   货品交货状况  1146 non-null   object 
     4   货品      1146 non-null   object 
     5   货品用户反馈  1146 non-null   object 
     6   销售区域    1146 non-null   object 
     7   数量      1146 non-null   float64
     8   销售金额    1146 non-null   object 
    dtypes: float64(1), object(8)
    memory usage: 89.5+ KB



```python
# 更新索引
data.reset_index(drop=True, inplace=True)
data.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 1146 entries, 0 to 1145
    Data columns (total 9 columns):
     #   Column  Non-Null Count  Dtype  
    ---  ------  --------------  -----  
     0   订单号     1146 non-null   object 
     1   销售时间    1146 non-null   object 
     2   交货时间    1146 non-null   object 
     3   货品交货状况  1146 non-null   object 
     4   货品      1146 non-null   object 
     5   货品用户反馈  1146 non-null   object 
     6   销售区域    1146 non-null   object 
     7   数量      1146 non-null   float64
     8   销售金额    1146 non-null   object 
    dtypes: float64(1), object(8)
    memory usage: 80.7+ KB



```python
data
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>订单号</th>
      <th>销售时间</th>
      <th>交货时间</th>
      <th>货品交货状况</th>
      <th>货品</th>
      <th>货品用户反馈</th>
      <th>销售区域</th>
      <th>数量</th>
      <th>销售金额</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>P096311</td>
      <td>2016-7-30</td>
      <td>2016-9-30</td>
      <td>晚交货</td>
      <td>货品3</td>
      <td>质量合格</td>
      <td>华北</td>
      <td>2.0</td>
      <td>1052,75元</td>
    </tr>
    <tr>
      <th>1</th>
      <td>P096826</td>
      <td>2016-8-30</td>
      <td>2016-10-30</td>
      <td>按时交货</td>
      <td>货品3</td>
      <td>质量合格</td>
      <td>华北</td>
      <td>10.0</td>
      <td>11,50万元</td>
    </tr>
    <tr>
      <th>2</th>
      <td>P097435</td>
      <td>2016-7-30</td>
      <td>2016-9-30</td>
      <td>按时交货</td>
      <td>货品1</td>
      <td>返修</td>
      <td>华南</td>
      <td>2.0</td>
      <td>6858,77元</td>
    </tr>
    <tr>
      <th>3</th>
      <td>P097446</td>
      <td>2016-11-26</td>
      <td>2017-1-26</td>
      <td>晚交货</td>
      <td>货品3</td>
      <td>质量合格</td>
      <td>华北</td>
      <td>15.0</td>
      <td>129,58元</td>
    </tr>
    <tr>
      <th>4</th>
      <td>P097446</td>
      <td>2016-11-26</td>
      <td>2017-1-26</td>
      <td>晚交货</td>
      <td>货品3</td>
      <td>拒货</td>
      <td>华北</td>
      <td>15.0</td>
      <td>32,39元</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1141</th>
      <td>P299901</td>
      <td>2016-12-15</td>
      <td>2017-3-15</td>
      <td>按时交货</td>
      <td>货品6</td>
      <td>质量合格</td>
      <td>马来西亚</td>
      <td>2.0</td>
      <td>200,41元</td>
    </tr>
    <tr>
      <th>1142</th>
      <td>P302956</td>
      <td>2016-12-22</td>
      <td>2017-3-22</td>
      <td>按时交货</td>
      <td>货品2</td>
      <td>拒货</td>
      <td>华东</td>
      <td>20.0</td>
      <td>79,44元</td>
    </tr>
    <tr>
      <th>1143</th>
      <td>P303801</td>
      <td>2016-12-15</td>
      <td>2017-3-15</td>
      <td>按时交货</td>
      <td>货品2</td>
      <td>质量合格</td>
      <td>华东</td>
      <td>1.0</td>
      <td>194,08元</td>
    </tr>
    <tr>
      <th>1144</th>
      <td>P307276</td>
      <td>2016-12-22</td>
      <td>2017-3-22</td>
      <td>按时交货</td>
      <td>货品6</td>
      <td>质量合格</td>
      <td>马来西亚</td>
      <td>1.0</td>
      <td>32,18元</td>
    </tr>
    <tr>
      <th>1145</th>
      <td>P314165</td>
      <td>2016-12-20</td>
      <td>2017-3-20</td>
      <td>按时交货</td>
      <td>货品2</td>
      <td>质量合格</td>
      <td>华东</td>
      <td>1.0</td>
      <td>1720,92元</td>
    </tr>
  </tbody>
</table>
<p>1146 rows × 9 columns</p>
</div>



#### 格式调整
取出销售金额列，对每一个数据进行清洗  
编写自定义过滤函数：删除逗号和元，转成float,如果是是万元还要*10000和删除万


```python
def data_deal(money):
    if(money.find('万元')!= -1):
        new_money = float(money[:money.find('万元')].replace(',',''))*10000 #取出数字删除逗号转换为float类型*10000
    else:
        new_money = float(money[:money.find('元')].replace(',','')) #取出数字删除逗号转换为float类型
    return new_money

data['销售金额'] = data['销售金额'].map(data_deal)
data
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>订单号</th>
      <th>销售时间</th>
      <th>交货时间</th>
      <th>货品交货状况</th>
      <th>货品</th>
      <th>货品用户反馈</th>
      <th>销售区域</th>
      <th>数量</th>
      <th>销售金额</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>P096311</td>
      <td>2016-7-30</td>
      <td>2016-9-30</td>
      <td>晚交货</td>
      <td>货品3</td>
      <td>质量合格</td>
      <td>华北</td>
      <td>2.0</td>
      <td>105275.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>P096826</td>
      <td>2016-8-30</td>
      <td>2016-10-30</td>
      <td>按时交货</td>
      <td>货品3</td>
      <td>质量合格</td>
      <td>华北</td>
      <td>10.0</td>
      <td>11500000.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>P097435</td>
      <td>2016-7-30</td>
      <td>2016-9-30</td>
      <td>按时交货</td>
      <td>货品1</td>
      <td>返修</td>
      <td>华南</td>
      <td>2.0</td>
      <td>685877.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>P097446</td>
      <td>2016-11-26</td>
      <td>2017-1-26</td>
      <td>晚交货</td>
      <td>货品3</td>
      <td>质量合格</td>
      <td>华北</td>
      <td>15.0</td>
      <td>12958.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>P097446</td>
      <td>2016-11-26</td>
      <td>2017-1-26</td>
      <td>晚交货</td>
      <td>货品3</td>
      <td>拒货</td>
      <td>华北</td>
      <td>15.0</td>
      <td>3239.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1141</th>
      <td>P299901</td>
      <td>2016-12-15</td>
      <td>2017-3-15</td>
      <td>按时交货</td>
      <td>货品6</td>
      <td>质量合格</td>
      <td>马来西亚</td>
      <td>2.0</td>
      <td>20041.0</td>
    </tr>
    <tr>
      <th>1142</th>
      <td>P302956</td>
      <td>2016-12-22</td>
      <td>2017-3-22</td>
      <td>按时交货</td>
      <td>货品2</td>
      <td>拒货</td>
      <td>华东</td>
      <td>20.0</td>
      <td>7944.0</td>
    </tr>
    <tr>
      <th>1143</th>
      <td>P303801</td>
      <td>2016-12-15</td>
      <td>2017-3-15</td>
      <td>按时交货</td>
      <td>货品2</td>
      <td>质量合格</td>
      <td>华东</td>
      <td>1.0</td>
      <td>19408.0</td>
    </tr>
    <tr>
      <th>1144</th>
      <td>P307276</td>
      <td>2016-12-22</td>
      <td>2017-3-22</td>
      <td>按时交货</td>
      <td>货品6</td>
      <td>质量合格</td>
      <td>马来西亚</td>
      <td>1.0</td>
      <td>3218.0</td>
    </tr>
    <tr>
      <th>1145</th>
      <td>P314165</td>
      <td>2016-12-20</td>
      <td>2017-3-20</td>
      <td>按时交货</td>
      <td>货品2</td>
      <td>质量合格</td>
      <td>华东</td>
      <td>1.0</td>
      <td>172092.0</td>
    </tr>
  </tbody>
</table>
<p>1146 rows × 9 columns</p>
</div>




```python
### 异常值处理
```


```python
data.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>数量</th>
      <th>销售金额</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>1146.000000</td>
      <td>1.146000e+03</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>76.069372</td>
      <td>1.223488e+05</td>
    </tr>
    <tr>
      <th>std</th>
      <td>589.416486</td>
      <td>1.114599e+06</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.000000</td>
      <td>0.000000e+00</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>1.000000</td>
      <td>2.941500e+03</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>1.000000</td>
      <td>9.476500e+03</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>4.000000</td>
      <td>3.576775e+04</td>
    </tr>
    <tr>
      <th>max</th>
      <td>11500.000000</td>
      <td>3.270000e+07</td>
    </tr>
  </tbody>
</table>
</div>



分析： 最小值的销售金额为0，该值为异常值，考虑删除。    
平均值和50%分位数 无论是数量还是销售金额都相差较大，数据严重右偏，电商领域2/8很正常，无需处理


```python
# 1、销售金额==0采用删除办法因为数据量较小
data = data[data['销售金额'] != 0]
data.describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>数量</th>
      <th>销售金额</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>1145.000000</td>
      <td>1.145000e+03</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>76.134934</td>
      <td>1.224557e+05</td>
    </tr>
    <tr>
      <th>std</th>
      <td>589.669861</td>
      <td>1.115081e+06</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1.000000</td>
      <td>5.100000e+01</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>1.000000</td>
      <td>2.946000e+03</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>1.000000</td>
      <td>9.486000e+03</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>4.000000</td>
      <td>3.577300e+04</td>
    </tr>
    <tr>
      <th>max</th>
      <td>11500.000000</td>
      <td>3.270000e+07</td>
    </tr>
  </tbody>
</table>
</div>



## 数据规整


```python
# 从销售时间中提取出月份
data['销售时间'] = pd.to_datetime(data['销售时间']) # 把销售时间转换为时间数据类型
# 新增一列存储月份
data['月份'] = data['销售时间'].map(lambda x: x.month)
data
```

    C:\Users\admin\AppData\Local\Temp\ipykernel_17948\2985495377.py:2: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      data['销售时间'] = pd.to_datetime(data['销售时间']) # 把销售时间转换为时间数据类型
    C:\Users\admin\AppData\Local\Temp\ipykernel_17948\2985495377.py:4: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      data['月份'] = data['销售时间'].map(lambda x: x.month)





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>订单号</th>
      <th>销售时间</th>
      <th>交货时间</th>
      <th>货品交货状况</th>
      <th>货品</th>
      <th>货品用户反馈</th>
      <th>销售区域</th>
      <th>数量</th>
      <th>销售金额</th>
      <th>月份</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>P096311</td>
      <td>2016-07-30</td>
      <td>2016-9-30</td>
      <td>晚交货</td>
      <td>货品3</td>
      <td>质量合格</td>
      <td>华北</td>
      <td>2.0</td>
      <td>105275.0</td>
      <td>7</td>
    </tr>
    <tr>
      <th>1</th>
      <td>P096826</td>
      <td>2016-08-30</td>
      <td>2016-10-30</td>
      <td>按时交货</td>
      <td>货品3</td>
      <td>质量合格</td>
      <td>华北</td>
      <td>10.0</td>
      <td>11500000.0</td>
      <td>8</td>
    </tr>
    <tr>
      <th>2</th>
      <td>P097435</td>
      <td>2016-07-30</td>
      <td>2016-9-30</td>
      <td>按时交货</td>
      <td>货品1</td>
      <td>返修</td>
      <td>华南</td>
      <td>2.0</td>
      <td>685877.0</td>
      <td>7</td>
    </tr>
    <tr>
      <th>3</th>
      <td>P097446</td>
      <td>2016-11-26</td>
      <td>2017-1-26</td>
      <td>晚交货</td>
      <td>货品3</td>
      <td>质量合格</td>
      <td>华北</td>
      <td>15.0</td>
      <td>12958.0</td>
      <td>11</td>
    </tr>
    <tr>
      <th>4</th>
      <td>P097446</td>
      <td>2016-11-26</td>
      <td>2017-1-26</td>
      <td>晚交货</td>
      <td>货品3</td>
      <td>拒货</td>
      <td>华北</td>
      <td>15.0</td>
      <td>3239.0</td>
      <td>11</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1141</th>
      <td>P299901</td>
      <td>2016-12-15</td>
      <td>2017-3-15</td>
      <td>按时交货</td>
      <td>货品6</td>
      <td>质量合格</td>
      <td>马来西亚</td>
      <td>2.0</td>
      <td>20041.0</td>
      <td>12</td>
    </tr>
    <tr>
      <th>1142</th>
      <td>P302956</td>
      <td>2016-12-22</td>
      <td>2017-3-22</td>
      <td>按时交货</td>
      <td>货品2</td>
      <td>拒货</td>
      <td>华东</td>
      <td>20.0</td>
      <td>7944.0</td>
      <td>12</td>
    </tr>
    <tr>
      <th>1143</th>
      <td>P303801</td>
      <td>2016-12-15</td>
      <td>2017-3-15</td>
      <td>按时交货</td>
      <td>货品2</td>
      <td>质量合格</td>
      <td>华东</td>
      <td>1.0</td>
      <td>19408.0</td>
      <td>12</td>
    </tr>
    <tr>
      <th>1144</th>
      <td>P307276</td>
      <td>2016-12-22</td>
      <td>2017-3-22</td>
      <td>按时交货</td>
      <td>货品6</td>
      <td>质量合格</td>
      <td>马来西亚</td>
      <td>1.0</td>
      <td>3218.0</td>
      <td>12</td>
    </tr>
    <tr>
      <th>1145</th>
      <td>P314165</td>
      <td>2016-12-20</td>
      <td>2017-3-20</td>
      <td>按时交货</td>
      <td>货品2</td>
      <td>质量合格</td>
      <td>华东</td>
      <td>1.0</td>
      <td>172092.0</td>
      <td>12</td>
    </tr>
  </tbody>
</table>
<p>1145 rows × 10 columns</p>
</div>



## 三数据分析并可视化
1、配送服务是否存在问题
### a.月份维度


```python
# 去掉首尾空格
data['货品交货状况'] = data['货品交货状况'].str.strip()
data_month = data.groupby(['月份','货品交货状况']).size().unstack() # unstack() 将月份设为行索引货物交货情况按时交货和晚交货设为列索引
data_month['按时交货货率'] = data_month['按时交货']/(data_month['按时交货']+data_month['晚交货'])
data_month

```

    C:\Users\admin\AppData\Local\Temp\ipykernel_17948\1538856180.py:2: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      data['货品交货状况'] = data['货品交货状况'].str.strip()





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>货品交货状况</th>
      <th>按时交货</th>
      <th>晚交货</th>
      <th>按时交货货率</th>
    </tr>
    <tr>
      <th>月份</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>7</th>
      <td>189</td>
      <td>13</td>
      <td>0.935644</td>
    </tr>
    <tr>
      <th>8</th>
      <td>218</td>
      <td>35</td>
      <td>0.861660</td>
    </tr>
    <tr>
      <th>9</th>
      <td>122</td>
      <td>9</td>
      <td>0.931298</td>
    </tr>
    <tr>
      <th>10</th>
      <td>238</td>
      <td>31</td>
      <td>0.884758</td>
    </tr>
    <tr>
      <th>11</th>
      <td>101</td>
      <td>25</td>
      <td>0.801587</td>
    </tr>
    <tr>
      <th>12</th>
      <td>146</td>
      <td>18</td>
      <td>0.890244</td>
    </tr>
  </tbody>
</table>
</div>



关键分析维度   
数量波动分析：11月交货总量显著下降（135)  
准时率趋势：  
整体准时率保持在80%以上  
11月准时率最低（80.16%）,整体第四季度比第三季度准时率低，可能存在季节性因素  

###  b.销售区域维度


```python
data_sal = data.groupby(['销售区域','货品交货状况']).size().unstack() # unstack() 将月份设为行索引货物交货情况按时交货和晚交货设为列索引
data_sal['按时交货货率'] = data_sal['按时交货']/(data_sal['按时交货']+data_sal['晚交货'])
data_sal
# 西北地区存在突出的延时交货问题急需解决
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>货品交货状况</th>
      <th>按时交货</th>
      <th>晚交货</th>
      <th>按时交货货率</th>
    </tr>
    <tr>
      <th>销售区域</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>华东</th>
      <td>268</td>
      <td>39</td>
      <td>0.872964</td>
    </tr>
    <tr>
      <th>华北</th>
      <td>226</td>
      <td>27</td>
      <td>0.893281</td>
    </tr>
    <tr>
      <th>华南</th>
      <td>10</td>
      <td>1</td>
      <td>0.909091</td>
    </tr>
    <tr>
      <th>泰国</th>
      <td>183</td>
      <td>4</td>
      <td>0.978610</td>
    </tr>
    <tr>
      <th>西北</th>
      <td>17</td>
      <td>44</td>
      <td>0.278689</td>
    </tr>
    <tr>
      <th>马来西亚</th>
      <td>310</td>
      <td>16</td>
      <td>0.950920</td>
    </tr>
  </tbody>
</table>
</div>



### c.货品维度


```python
data_goods = data.groupby(['货品','货品交货状况']).size().unstack() # unstack() 将月份设为行索引货物交货情况按时交货和晚交货设为列索引
data_goods['按时交货货率'] = data_goods['按时交货']/(data_goods['按时交货']+data_goods['晚交货'])
data_goods.sort_values(by='按时交货货率',ascending=False)
#货品4存在突出的延时交货问题急需解决，货品2的问题也需要注意。
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>货品交货状况</th>
      <th>按时交货</th>
      <th>晚交货</th>
      <th>按时交货货率</th>
    </tr>
    <tr>
      <th>货品</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>货品5</th>
      <td>183</td>
      <td>4</td>
      <td>0.978610</td>
    </tr>
    <tr>
      <th>货品6</th>
      <td>309</td>
      <td>7</td>
      <td>0.977848</td>
    </tr>
    <tr>
      <th>货品1</th>
      <td>27</td>
      <td>2</td>
      <td>0.931034</td>
    </tr>
    <tr>
      <th>货品3</th>
      <td>212</td>
      <td>26</td>
      <td>0.890756</td>
    </tr>
    <tr>
      <th>货品2</th>
      <td>269</td>
      <td>48</td>
      <td>0.848580</td>
    </tr>
    <tr>
      <th>货品4</th>
      <td>14</td>
      <td>44</td>
      <td>0.241379</td>
    </tr>
  </tbody>
</table>
</div>



### d.货品和销售区域结合


```python
data_goods_sal = data.groupby(['货品','销售区域','货品交货状况']).size().unstack(fill_value=0) # unstack() 将月份设为行索引货物交货情况按时交货和晚交货设为列索引
data_goods_sal['按时交货货率'] = data_goods_sal['按时交货']/(data_goods_sal['按时交货']+data_goods_sal['晚交货'])
data_goods_sal.sort_values(by='按时交货货率',ascending=False)
# 销售区域：西北地区延时交货问题,主要是货品4晚交货导致
# 货品：最差的货品2送往马来西亚的晚送货问题突出
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>货品交货状况</th>
      <th>按时交货</th>
      <th>晚交货</th>
      <th>按时交货货率</th>
    </tr>
    <tr>
      <th>货品</th>
      <th>销售区域</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>货品1</th>
      <th>西北</th>
      <td>3</td>
      <td>0</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>货品5</th>
      <th>泰国</th>
      <td>183</td>
      <td>4</td>
      <td>0.978610</td>
    </tr>
    <tr>
      <th>货品6</th>
      <th>马来西亚</th>
      <td>309</td>
      <td>7</td>
      <td>0.977848</td>
    </tr>
    <tr>
      <th rowspan="2" valign="top">货品1</th>
      <th>华北</th>
      <td>14</td>
      <td>1</td>
      <td>0.933333</td>
    </tr>
    <tr>
      <th>华南</th>
      <td>10</td>
      <td>1</td>
      <td>0.909091</td>
    </tr>
    <tr>
      <th>货品3</th>
      <th>华北</th>
      <td>212</td>
      <td>26</td>
      <td>0.890756</td>
    </tr>
    <tr>
      <th>货品2</th>
      <th>华东</th>
      <td>268</td>
      <td>39</td>
      <td>0.872964</td>
    </tr>
    <tr>
      <th>货品4</th>
      <th>西北</th>
      <td>14</td>
      <td>44</td>
      <td>0.241379</td>
    </tr>
    <tr>
      <th>货品2</th>
      <th>马来西亚</th>
      <td>1</td>
      <td>9</td>
      <td>0.100000</td>
    </tr>
  </tbody>
</table>
</div>



### 2、是否存在尚有潜力的销售区域
a.月份维度


```python
data1 = data.groupby(['月份','货品'])['数量'].sum().unstack(fill_value=0) # unstack() 将月份设为行索引货物交货情况按时交货和晚交货设为列索引
data1.plot(kind='line') # 货品2在10月和12月销量猛增原因猜测有二1、公司加大营销力度，2、开发了新的市场
```




    <Axes: xlabel='月份'>




​    
![png](output_30_1.png)
​    


### b.不同区域


```python
data1 = data.groupby(['销售区域','货品'])['数量'].sum().unstack(fill_value=0)
data1
# 从销售区域看每种货品的销售区域为1-3个货品1有三个销售区域，货品2有2个销售区域其他货品销售区域都是一个
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th>货品</th>
      <th>货品1</th>
      <th>货品2</th>
      <th>货品3</th>
      <th>货品4</th>
      <th>货品5</th>
      <th>货品6</th>
    </tr>
    <tr>
      <th>销售区域</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>华东</th>
      <td>0.0</td>
      <td>53811.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>华北</th>
      <td>2827.0</td>
      <td>0.0</td>
      <td>9073.5</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>华南</th>
      <td>579.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>泰国</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>5733.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>西北</th>
      <td>11.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>5229.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>马来西亚</th>
      <td>0.0</td>
      <td>1510.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>8401.0</td>
    </tr>
  </tbody>
</table>
</div>



### c.月份和区域


```python
data1 = data.groupby(['月份','销售区域','货品'])['数量'].sum().unstack(fill_value=0)
data1['货品2']
#货品2在10，12月份销量猛增，原因主要发生在原有销售区域(华东
#同样，分析出在7，8，9，11月份销售数量还有很大提升空间，可以适当加大营销力度
```




    月份  销售区域
    7   华东        489.0
        华北          0.0
        华南          0.0
        泰国          0.0
        西北          0.0
        马来西亚        2.0
    8   华东       1640.0
        华北          0.0
        华南          0.0
        泰国          0.0
        西北          0.0
        马来西亚     1503.0
    9   华东       3019.0
        华北          0.0
        华南          0.0
        泰国          0.0
        西北          0.0
        马来西亚        1.0
    10  华东      28420.0
        华北          0.0
        泰国          0.0
        西北          0.0
        马来西亚        0.0
    11  华东       2041.0
        华北          0.0
        华南          0.0
        泰国          0.0
        西北          0.0
        马来西亚        1.0
    12  华东      18202.0
        华北          0.0
        华南          0.0
        泰国          0.0
        西北          0.0
        马来西亚        3.0
    Name: 货品2, dtype: float64



### 3、商品是否存在质量问题


```python
data['货品用户反馈'] = data['货品用户反馈'].str.strip() #去除首尾空格
data1 = data.groupby(['货品','销售区域'])['货品用户反馈'].value_counts().unstack(fill_value=0)
data1['拒货率'] = round(data1['拒货']/data1.sum(axis=1),2)
data1['合格率'] = round(data1['质量合格']/data1.sum(axis=1),2)
data1['返修率'] = round(data1['返修']/data1.sum(axis=1),2)
```

    C:\Users\admin\AppData\Local\Temp\ipykernel_17948\1439624083.py:1: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      data['货品用户反馈'] = data['货品用户反馈'].str.strip() #去除首尾空格



```python
data1.sort_values(['合格率','返修率','拒货率'],ascending=False)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>货品用户反馈</th>
      <th>拒货</th>
      <th>质量合格</th>
      <th>返修</th>
      <th>拒货率</th>
      <th>合格率</th>
      <th>返修率</th>
    </tr>
    <tr>
      <th>货品</th>
      <th>销售区域</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>货品3</th>
      <th>华北</th>
      <td>31</td>
      <td>188</td>
      <td>19</td>
      <td>0.13</td>
      <td>0.79</td>
      <td>0.08</td>
    </tr>
    <tr>
      <th>货品6</th>
      <th>马来西亚</th>
      <td>56</td>
      <td>246</td>
      <td>14</td>
      <td>0.18</td>
      <td>0.78</td>
      <td>0.04</td>
    </tr>
    <tr>
      <th>货品5</th>
      <th>泰国</th>
      <td>14</td>
      <td>144</td>
      <td>29</td>
      <td>0.07</td>
      <td>0.77</td>
      <td>0.15</td>
    </tr>
    <tr>
      <th>货品2</th>
      <th>华东</th>
      <td>72</td>
      <td>184</td>
      <td>51</td>
      <td>0.23</td>
      <td>0.60</td>
      <td>0.17</td>
    </tr>
    <tr>
      <th rowspan="3" valign="top">货品1</th>
      <th>华南</th>
      <td>5</td>
      <td>4</td>
      <td>2</td>
      <td>0.45</td>
      <td>0.35</td>
      <td>0.17</td>
    </tr>
    <tr>
      <th>西北</th>
      <td>0</td>
      <td>1</td>
      <td>2</td>
      <td>0.00</td>
      <td>0.33</td>
      <td>0.60</td>
    </tr>
    <tr>
      <th>华北</th>
      <td>0</td>
      <td>3</td>
      <td>12</td>
      <td>0.00</td>
      <td>0.20</td>
      <td>0.79</td>
    </tr>
    <tr>
      <th>货品4</th>
      <th>西北</th>
      <td>0</td>
      <td>9</td>
      <td>49</td>
      <td>0.00</td>
      <td>0.16</td>
      <td>0.84</td>
    </tr>
    <tr>
      <th>货品2</th>
      <th>马来西亚</th>
      <td>6</td>
      <td>1</td>
      <td>3</td>
      <td>0.60</td>
      <td>0.09</td>
      <td>0.28</td>
    </tr>
  </tbody>
</table>
</div>




```python
#货品3.6.5合格率均较高，返修率比较低，说明质量还可以
#货品1.2.4合格率较低，返修率较高，质量存在一定的问题，需要改善
#货品2在马拉西亚的把货率最高，同时，在货品2在马拉西亚的按时交货率也非常低。猜测:马来西亚入对送货的时效性要求较高,如果达不到，则往往考虑拒货。
#考虑到货品2主要在华东地区销售量大，可以考虑增大在华东的投资，适当减少马来西亚的投入。
```


```python

```


```python

```
