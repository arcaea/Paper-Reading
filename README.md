# Paper-Reading
Paper：<strong>Rare Events Analysis for High‐Frequency Equity Data</strong> \
Author：Dragos Bozdog, Ionut¸ Florescu, Khaldoun Khashanah, and Jim Wang \
Stevens Institute of Technology, e-mail: ifl oresc@stevens.edu


## 原文片段
A thorough investigation of the distribution of price changes, conditional on cumulative trading window, would involve the evaluation of all observations for each equity (S<sub>n</sub>-S<sub>j</sub> | V<sub>k</sub>+V<sub>k+1</sub>+...+V<sub>n</sub><V<sub>0</sub>) for k ≤ j ≤ n where n runs through all the trades, S<sub>n</sub> is the price, and V<sub>n</sub> is the volume associated with trade n. Although the distribution would be accurate, such a task would involve significant computational effort considering the large database we use. Because we are interested only in the rare events, we chose to select the extreme observations in this sequence and subsequently analyze only a percentage of these observations at the tails of the distribution Δp | V<V<sub>n</sub>. \
Specifically, we construct the sequence of consecutive trades S<sub>k</sub>,S<sub>k+1</sub>,...,S<sub>n</sub> and their associated volumes V<sub>k</sub>,V<sub>k,1</sub>,...,V<sub>n</sub>such that V<sub>k</sub>+V<sub>k+1</sub>+...+V<sub>n</sub><V<sub>0</sub> and we consider Δp<sub>n</sub>=max{S<sub>n</sub>-S<sub>k</sub>,S<sub>n</sub>-S<sub>k+1</sub>,S<sub>n</sub>-S<sub>n-1</sub>}


## 以程式碼去寫出下圖2個方程式
### S<sub>n</sub>-S<sub>j</sub>：
![image](https://github.com/arcaea/Paper-Reading/blob/main/Pics/Sn-Sj.PNG)
### Δp<sub>n</sub>：
![image](https://github.com/arcaea/Paper-Reading/blob/main/Pics/max.PNG)


## python使用環境
google colaboratory \
硬體加速器：None


## 流程介紹
### 流程1-安裝管理套件工具
```python
!pip install yfinance
```
### 流程2-匯入模組

```python
import math
import numpy as np
import pandas as pd
import yfinance as yf
from datetime import timedelta,datetime,date
```
### 流程3-導入市場數據
從yahoo財經導入從 2021/11/15 到 2022/11/15 的APPLE市場數據。
```python
ticker="AAPL" #APPLE市場數據

enddate=datetime.strptime('2022/11/15','%Y/%m/%d').date() #截止日期
startdate=(enddate-timedelta(days=365)) #起始日期

data=yf.download(tickers=ticker,start=startdate,end=enddate,interval="1d")#按每一天時間間隔獲取數據

data.columns #顯示導入結果的欄位
```
### 流程4-將所需資料存進新的dataframe裡
只需Adj Close與Volume的資料，所以將其存進新的變數data_s
```python
data_s = data[['Adj Close','Volume']]

data_s #顯示導入結果
```
### 流程5-存日期
將date存進data_s的dataframe裡
```python
dates=[] #建立空字串-儲存日期

for row in data_s.index: 
  dates.append(str(row)[0:10]) #將data_s的index只有年月日的部分存入dates字串裡
data_s['Date']=dates #將dates字串裡的資料存進data_s裡

data_s #顯示導入結果
```
### 流程6-取資料
將data_s的所有資料分別存進新變數裡
```python
Close=list(data_s.iloc[:,0])#將Close存在Adj Close中
Volume=list(data_s.iloc[:,1])#將Volumn存在volume中
Date=list(data_s.iloc[:,2])#將Date存在Date中
```
### 流程7-設定V<sub>0</sub>
任意設定一個V<sub>0</sub>值，做了2個V<sub>0</sub>測試(V<sub>0</sub>取平均值與V<sub>0</sub>取5%以下的最低值)
```python
# V0=np.mean(Volume)#V0取平均值
V0=np.sort(Volume)[math.ceil(len(data_s)*0.05)]#V0取5%以下的最低值

print("V0="+str(V0))#輸出V0的值
```
### 流程8-找出S<sub>n</sub>-S<sub>j</sub>的值、S<sub>n</sub>日期和S<sub>j</sub>日期
找出所有達成V<sub>k</sub>+V<sub>k+1</sub>+...+V<sub>n</sub><V<sub>0</sub>條件下的S<sub>n</sub>-S<sub>j</sub> (k≤j≤n)的值、S<sub>n</sub>日期和S<sub>j</sub>日期
```python
var_k=[]#建立空字串-儲存Sn-Sj
var_date=[]#建立空字串-儲存Sn的日期與Sj的日期
n=len(data_s)-1#資料長度

for i in range(0,n):#所有日期
  s=0#總和歸0
  for j in range(i):#在i日期前的所有日期
    s=s+Volume[j]#總和(Vk+Vk+1+...+Vn)
    if(s<V0):#判斷是否達成條件
      var_k.append(Close[n]-Close[i])#將Sn-Sj存入字串
      var_date.append([Date[n],Date[i]])#將Sn日期和Sj日期存入字串

print(var_k)#顯示所有的Sn-Sj
print(var_date)#顯示所有的Sn日期和Sj日期
```
### 流程9-找出S<sub>n</sub>-S<sub>j</sub>的值、S<sub>n</sub>日期和S<sub>j</sub>日期
找出S<sub>n</sub>-S<sub>j</sub>裡的最大值與當天的S<sub>n</sub>日期和S<sub>j</sub>日期
```python
print(max(var_k))#Sn-Sj的最大值

index = var_k.index(max(var_k))#找出當天日期的索引值
print(var_date[index])#找出當天日期
```
