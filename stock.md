# Stock market price histories

```python
#Import needed library.
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
import yfinance as yf

#Styling.
sns.set_style('whitegrid')
```

## Let's examine the price histories of two stocks.

```python
#Bring Apple's and NVIDIA's datas from Yahoo Finance, starting from 2018.
apple = yf.download('AAPL.NE', start = '2023-01-01', auto_adjust=False)
nvidia = yf.download('NVDA.NE', start = '2023-01-01', auto_adjust=False)
apple
```

<img width="587" height="506" alt="image" src="https://github.com/user-attachments/assets/f5b123a6-52a7-4bd5-ba09-33873e7f8dcf" />

```python
#Plot darily closing prices for Apple.
apple['Close'].plot()
```

<img width="563" height="426" alt="image" src="https://github.com/user-attachments/assets/8ebcd77f-7577-4ca9-89bf-2b4fbcba45f1" />

```python
#Plot darily closing prices for NVIDIA.
nvidia['Close'].plot()
```
<img width="556" height="434" alt="image" src="https://github.com/user-attachments/assets/bb371492-09cf-4f2d-acb2-4497d96e6722" />

```python
#Put the Apple's and NVIDIA's plots next to each other.
fig, axs = plt.subplots(nrows=1, ncols=2, figsize = (12, 6))
apple['Close'].plot(ax=axs[0], label = 'Apple')
nvidia['Close'].plot(ax=axs[1], label = 'Nvidia')
```

<img width="997" height="520" alt="image" src="https://github.com/user-attachments/assets/1fb006d5-2e29-427f-927b-f53d6974d0d7" />

Both of the stocks' values have increased.

```python
#Plot that closing prices monthly averages.
fig, axs = plt.subplots(nrows=1, ncols=2, figsize = (12, 6))
apple['Close'].resample('ME').mean().plot(ax=axs[0], label = 'Apple')
nvidia['Close'].resample('ME').mean().plot(ax=axs[1], label = 'Nvidia')
```

<img width="986" height="559" alt="image" src="https://github.com/user-attachments/assets/c5c14987-aa7e-49fb-a15e-333bb13e7eac" />

```python
#Plot quarterly trade values.
fig, axs = plt.subplots(nrows=1, ncols=2, figsize = (12, 6))
apple['Close'].resample('QE').mean().plot(ax=axs[0], label = 'Apple')
nvidia['Close'].resample('QE').mean().plot(ax=axs[1], label = 'Nvidia')
```

<img width="990" height="558" alt="image" src="https://github.com/user-attachments/assets/9053d707-6b9d-49e2-9869-500322937898" />

Both of the stocks' trade volumes have increased.

```python
#Plot darily closing prices and 50 and 200 days moving averages.
#For Apple first.
fig, axs = plt.subplots(nrows=1, ncols=1, figsize = (12, 6))
apple['Close'].plot(ax=axs, label = 'Apple')
apple['Close'].rolling(50).mean().plot(ax=axs)
apple['Close'].rolling(200).mean().plot(ax=axs)
plt.ylabel('Apple')
plt.legend(['Darily closing prices', '50 days moving average', '200 days moving average'])
plt.xlim(['2023', '2025-08'])
```

<img width="1016" height="516" alt="image" src="https://github.com/user-attachments/assets/c46ab42e-d19b-4269-892c-e73aa4f73820" />

```python
#Plot darily closing prices and 50 and 200 days moving averages.
#For NVIDIA.
fig, axs = plt.subplots(nrows=1, ncols=1, figsize = (12, 6))
nvidia['Close'].plot(ax=axs, label = 'NVDIA')
nvidia['Close'].rolling(50).mean().plot(ax=axs)
nvidia['Close'].rolling(200).mean().plot(ax=axs)
plt.ylabel('NVIDIA')
plt.legend(['Darily closing prices', '50 days moving average', '200 days moving average'])
plt.xlim(['2023', '2025-08'])
```

<img width="1017" height="516" alt="image" src="https://github.com/user-attachments/assets/dd4a8562-685f-420a-8a1d-89f96b27d4b6" />

```python
#Counting % changes for Apple and NVIDIA stocks' prices.
#Add a % changing column to Apple and NVIDIA data frames.
apple['Apple Change %'] = apple['Close'].pct_change()
nvidia['NVIDIA Change %'] = nvidia['Close'].pct_change()
apple.head()
```

<img width="713" height="249" alt="image" src="https://github.com/user-attachments/assets/02ea2400-3d7a-4b48-9780-47fecd4ebf36" />

The first value in the % change column is missing, because there is no previous value to compare to.

```python
#Let's make a new data frame with only the % change column.
changes = pd.concat([apple['Apple Change %'], nvidia['NVIDIA Change %']], axis=1)

#Replace the missing values with 0.
changes = changes.fillna(0)
changes
```

<img width="373" height="440" alt="image" src="https://github.com/user-attachments/assets/d2cedc72-2f99-4d80-87f8-3eac2733db00" />

```python
#Plot & changes for August 2025.
(changes.loc['2025-08-01':]*100).plot(kind='bar')
plt.ylabel('% Changes')
plt.axhline(color='yellow')
```

<img width="566" height="581" alt="image" src="https://github.com/user-attachments/assets/d57b5838-dd4c-4928-8eb9-bbb2633b31b4" />

```python
#Calculating statistical key values for % changes.
(changes*100).describe().round(2)
```

<img width="346" height="285" alt="image" src="https://github.com/user-attachments/assets/2bee53bf-d529-46cd-8d9e-d41844b3dab6" />

NVIDIA's range of % changes is bigger than Apple's.

```python
#Calculating correlations for % changes.
changes.corr()
```

<img width="429" height="101" alt="image" src="https://github.com/user-attachments/assets/36b931d3-8234-44c6-9e79-d72dbcdecc63" />

The correlation of % changes is 0.378, which is a weak positive correlation.

```python
#Show scatterplots of % changes.
sns.scatterplot(data=changes, x='Apple Change %', y='NVIDIA Change %')
plt.xlabel('Apple % changes')
plt.ylabel('NVIDIA % changes')
```

<img width="581" height="454" alt="image" src="https://github.com/user-attachments/assets/8f9e4991-fd8b-4a5e-81f6-9744ecf8dd4f" />

```python
#Plot moving correlation with 100 values.
changes['Apple Change %'].rolling(100).corr(changes['NVIDIA Change %']).plot()
plt.ylabel('Moving Correlation')
plt.axhline(color='yellow')
```

<img width="575" height="425" alt="image" src="https://github.com/user-attachments/assets/de9dbe14-bc0f-49a5-b095-1f190572f978" />

Moving correlation is positive during the whole time peirod, but there is a lot of variabilities.

```python
#Plot moving volatility with 200 day window.
plt.figure(figsize=(12, 6))
(changes['Apple Change %'].rolling(200).std()*(252**0.5)).plot(label='Apple', legend=True)
plt.ylabel('Moving Volatility')
(changes['NVIDIA Change %'].rolling(200).std()*(252**0.5)).plot(label='NVIDIA', legend=True)
plt.axhline(color='yellow')
```

<img width="1011" height="511" alt="image" src="https://github.com/user-attachments/assets/fe4563ee-9f5e-4222-af7b-926176944b46" />

NVIDIA's moving volatility is higher than Apple's during the whole time period.
