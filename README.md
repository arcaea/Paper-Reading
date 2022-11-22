# Paper-Reading
Paper：Portfolio Choice Based on Third-Degree Stochastic Dominance

.............. \


# 環境
google colaboratory \
Hardware accelerator：None

# 流程介紹
## 流程1-安裝管理套件工具
```python
!pip install yfinance
```
## 流程2-匯入模組

```python
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
任意設定一個V0值，做了2個V0測試(V0取平均值與V0取5%以下的最低值)
```python
import math

# V0為平均值
# V0=np.mean(Volume)
# print("V0="+str(V0))

#V0 take the lowest value under 5%
V0=np.sort(Volume)[math.ceil(len(data_s)*0.05)]
print("V0="+str(V0))
```
