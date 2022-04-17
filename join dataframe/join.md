```python
import pandas as pd
import numpy as np
import datetime
```

## 先给数据都加上以月为单位的时间index


```python
times = pd.date_range(periods=165, freq='M', end='2020/5')

times = times.to_list()
times.reverse()

times =pd.to_datetime(times)
times = times.strftime('%Y-%m')

df = pd.read_excel(r'data by month.xlsx')
df = df.set_index(times)

df.to_excel(r'./1.xlsx')
```

## 对于合并的基准df，需要新增加一列作为排序的标准
如果不这样的话，join后的结果顺序会出问题


```python
df1 = pd.read_excel(r'./data by week.xlsx', header=1, usecols=range(0,4))

tmp = pd.to_datetime(df1["时间(该日期所在“周”的煤炭价格)年/月/日"])
tmp = tmp.dt.strftime('%Y-%m-%d')

df1["时间(该日期所在“周”的煤炭价格)年/月/日"]= pd.to_datetime(df1["时间(该日期所在“周”的煤炭价格)年/月/日"])
df1["时间(该日期所在“周”的煤炭价格)年/月/日"] = df1["时间(该日期所在“周”的煤炭价格)年/月/日"].dt.strftime('%Y-%m')
df1['num'] = pd.Series(data=np.arange(0,len(df1)), index=df1.index)
df1.set_index("时间(该日期所在“周”的煤炭价格)年/月/日", inplace=True)
```


```python
df.head(3)
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
      <th>山西</th>
      <th>内蒙古</th>
      <th>陕西</th>
      <th>发电量</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2020-04</th>
      <td>8595.4</td>
      <td>8527.6</td>
      <td>5600.6</td>
      <td>190.1</td>
    </tr>
    <tr>
      <th>2020-03</th>
      <td>9479.4</td>
      <td>8906.1</td>
      <td>5710.2</td>
      <td>201.1</td>
    </tr>
    <tr>
      <th>2020-02</th>
      <td>7596.0</td>
      <td>8422.0</td>
      <td>5490.0</td>
      <td>225.0</td>
    </tr>
  </tbody>
</table>
</div>




```python
df1.head(3)
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
      <th>价格低值(元/吨)</th>
      <th>价格高值(元/吨)</th>
      <th>价格平均值(元/吨)</th>
      <th>num</th>
    </tr>
    <tr>
      <th>时间(该日期所在“周”的煤炭价格)年/月/日</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2020-04</th>
      <td>470</td>
      <td>480</td>
      <td>475.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2020-04</th>
      <td>475</td>
      <td>485</td>
      <td>480.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2020-04</th>
      <td>490</td>
      <td>495</td>
      <td>492.5</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>



## 利用df.join()合并，之后排序


```python
result = df1.join(df)
result.sort_values(by='num', ascending=True, inplace=True)
result.drop(columns='num', axis=1, inplace=True)
result.set_index(tmp, inplace=True)
result.to_excel(r'./2.xlsx')
result.head()
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
      <th>价格低值(元/吨)</th>
      <th>价格高值(元/吨)</th>
      <th>价格平均值(元/吨)</th>
      <th>山西</th>
      <th>内蒙古</th>
      <th>陕西</th>
      <th>发电量</th>
    </tr>
    <tr>
      <th>时间(该日期所在“周”的煤炭价格)年/月/日</th>
      <th></th>
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
      <th>2020-04-30</th>
      <td>470</td>
      <td>480</td>
      <td>475.0</td>
      <td>8595.4</td>
      <td>8527.6</td>
      <td>5600.6</td>
      <td>190.1</td>
    </tr>
    <tr>
      <th>2020-04-24</th>
      <td>475</td>
      <td>485</td>
      <td>480.0</td>
      <td>8595.4</td>
      <td>8527.6</td>
      <td>5600.6</td>
      <td>190.1</td>
    </tr>
    <tr>
      <th>2020-04-17</th>
      <td>490</td>
      <td>495</td>
      <td>492.5</td>
      <td>8595.4</td>
      <td>8527.6</td>
      <td>5600.6</td>
      <td>190.1</td>
    </tr>
    <tr>
      <th>2020-04-10</th>
      <td>505</td>
      <td>515</td>
      <td>510.0</td>
      <td>8595.4</td>
      <td>8527.6</td>
      <td>5600.6</td>
      <td>190.1</td>
    </tr>
    <tr>
      <th>2020-04-03</th>
      <td>530</td>
      <td>535</td>
      <td>532.5</td>
      <td>8595.4</td>
      <td>8527.6</td>
      <td>5600.6</td>
      <td>190.1</td>
    </tr>
  </tbody>
</table>
</div>


