# Paper-Reading
Paper：Portfolio Choice Based on Third-Degree Stochastic Dominance

.............. \


# 環境
google colaboratory \
Hardware accelerator：None

# 流程1-安裝管理套件工具
```python
!pip install yfinance
```
# 流程2-匯入模組

```python
import numpy as np
import pandas as pd
import yfinance as yf
from datetime import timedelta,datetime,date
```
# 流程3-下載市場數據
從yahoo財經下載從2021/11/15到2022/11/15的APPLE市場數據。
```python
ticker="AAPL" #APPLE市場數據

enddate=datetime.strptime('2022/11/15','%Y/%m/%d').date() #截止日期
startdate=(enddate-timedelta(days=365)) #起始日期

data=yf.download(tickers=ticker,start=startdate,end=enddate,interval="1d")

data.columns
```
