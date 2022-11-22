# Paper-Reading
Paper：Rare Events Analysis for High‐Frequency Equity Data

.............. \


# python使用環境
google colaboratory \
Hardware accelerator：None

# 流程介紹
## 流程1-安裝管理套件工具
```python
!pip install yfinance
```
## 流程2-匯入模組

```python
import math
import numpy as np
import pandas as pd
import yfinance as yf
from datetime import timedelta,datetime,date
```
## 流程3-導入市場數據
從yahoo財經導入從 2021/11/15 到 2022/11/15 的APPLE市場數據。
```python
ticker="AAPL" #APPLE市場數據

enddate=datetime.strptime('2022/11/15','%Y/%m/%d').date() #截止日期
startdate=(enddate-timedelta(days=365)) #起始日期

data=yf.download(tickers=ticker,start=startdate,end=enddate,interval="1d")

data.columns #顯示導入結果
```
## 流程3-將所需資料存進新的dataframe裡
只需Adj Close與Volume的資料，所以將其存進新的變數data_s
```python
data_s = data[['Adj Close','Volume']]

data_s #顯示導入結果
```
## 流程4-存日期
將date存進data_s的dataframe裡
```python
dates=[] #建立空字串

for row in data_s.index: 
  dates.append(str(row)[0:10]) #將data_s的index只有年月日的部分存入dates字串裡
data_s['Date']=dates #將dates字串裡的資料存進data_s裡

data_s #顯示導入結果
```
## 流程5-取資料
將data_s的所有資料分別存進新變數裡
```python
Close=list(data_s.iloc[:,0])#將Close存在Adj Close中
Volume=list(data_s.iloc[:,1])#將Volumn存在volume中
Date=list(data_s.iloc[:,2])#將Date存在Date中
```
## 流程6-設定V0
任意設定一個V<sub>0</sub>值，做了2個V<sub>0</sub>測試(V0取平均值與V0取5%以下的最低值)
```python
# V0=np.mean(Volume)#V0取平均值
V0=np.sort(Volume)[math.ceil(len(data_s)*0.05)]#V0取5%以下的最低值

print("V0="+str(V0))#輸出V0的值
```
## 流程7-
找出所有達成V<sub>k</sub>+V<sub>k+1</sub>+V<sub>n</sub><V<sub>0</sub>條件下的S<sub>n</sub>-S<sub>j</sub>，k≤j≤n的值
```python
var_k=[]#建立空字串-儲存Sn-
var_date=[]#建立空字串
n=len(data_s)-1

for i in range(0,n):
  s=0
  for j in range(i):
    s=s+Volume[j]
    if(s<V0):
      var_k.append(Close[n]-Close[i])
      var_date.append([Date[n],Date[i]])

print(var_k)
print(var_date)
```
