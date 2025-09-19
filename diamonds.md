# Descrptive Analytics - Diamonds Dataset

Author: Hsuan-Yu Chen

Created: 2025.09.18

[Link to the original google colab file](https://colab.research.google.com/drive/1nXiR_7rLXrJSFKFJmEhg14e5rRnSzbxX?usp=sharing)

## Importing Libraries

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
sns.set_style('white')
```

# Getting to know the dataset

Show available dataset from seaborn library.

```python
sns.get_dataset_names()
```

<img width="160" height="391" alt="image" src="https://github.com/user-attachments/assets/9c23eb75-12e2-4bfc-8fb4-39cf92754ee8" />

Diamonds dataset was chosen.

```python
#Load diamonds dataset and show first five rows.
df = sns.load_dataset('diamonds')
df.head()
```

<img width="588" height="192" alt="image" src="https://github.com/user-attachments/assets/3b7c30eb-3799-4ba4-95d5-ede865ba4ed1" />

```python
#Show information about dataframe columns. 
df.info()
```

<img width="354" height="319" alt="image" src="https://github.com/user-attachments/assets/09cf0b77-a15d-45d8-ac96-376657d1380f" />

```python
#Show 53940 rows, 10 columns.
df.shape
```

<img width="103" height="24" alt="image" src="https://github.com/user-attachments/assets/e2b769e2-8295-430a-8b11-0c53610c9c18" />

```python
#See values that category variables have.
dfhelper = df[['cut', 'color', 'clarity']]
for x in dfhelper:
  print(x, df[x].unique())
```

<img width="678" height="112" alt="image" src="https://github.com/user-attachments/assets/c0ff3fa4-42cc-4bd6-9e1d-a115e2e3efa4" />

# Filter the data

```python
#Do the filter to the dataset.
#Show diamond where carat>0.30 & cut==Good & color==J & price>12000.
df[(df['carat']>0.30)&(df['cut']=='Good')&(df['color']=='J')&(df['price']>12000)]
```

<img width="591" height="628" alt="image" src="https://github.com/user-attachments/assets/29caef20-98b1-4d28-b0b1-d520dd1047f7" />

# Frequency of diamond colors

```python
#Make a frequency table of diamond color.
dfcolor = pd.crosstab(df['color'], 'f')
#Add % column.
n_color = dfcolor['f'].sum()
dfcolor['%'] = dfcolor['f']/n_color*100
#Remove col_0.
dfcolor.columns.name = ''
dfcolor.style.format({'f' : '{:.0f}', '%' : '{:.1f}'})
```

<img width="166" height="289" alt="image" src="https://github.com/user-attachments/assets/f0dd45ad-3dbf-45ae-b19d-47e0ddf227ab" />

```python
#Show the frequency table as a barchat.
dfcolor['%'].plot(kind='barh')
plt.grid(axis='x')
plt.xlabel(f'%, n={n_color}')
plt.show()
```

<img width="554" height="425" alt="image" src="https://github.com/user-attachments/assets/ad97bc9a-d4d3-4996-b7f7-0ffc55050703" />

# Prices' distributions of diamonds

```python
#Find key values of diamonds' prices'.
df['price'].describe()
```

<img width="173" height="318" alt="image" src="https://github.com/user-attachments/assets/6d1e0079-21cb-45fb-8059-94f97da51ed5" />

```python
#Make a categorical variables call priceclass.
#Make different bounds of prices.
bound = [0,3000,6000,9000,12000,15000,18000,21000]
#We add a new column in data frame.
df['priceclass'] = pd.cut(df['price'], bins=bound, right=False)
df.head()
```

<img width="683" height="191" alt="image" src="https://github.com/user-attachments/assets/82c913b1-df2e-4de1-af5d-6b5163ce2a06" />

```python
#Make a frequency table of priceclass.
dfpriceclass = pd.crosstab(df['priceclass'], 'f')
#Add % column.
n_priceclass = dfpriceclass['f'].sum()
dfpriceclass['%'] = dfpriceclass['f']/n_priceclass*100
dfpriceclass.loc['total'] = dfpriceclass.sum()
dfpriceclass.style.format({'f' : '{:.0f}', '%' : '{:.1f}'})
```

<img width="223" height="314" alt="image" src="https://github.com/user-attachments/assets/2a3f9e47-69c9-4342-a297-1d34090812fa" />

```python
#Make histogram of price distribution.
sns.histplot(df['price'], bins=bound)
plt.xlabel('price')
plt.ylabel(f'count, n = {n_priceclass}')
plt.grid(axis='y')
plt.show()
```

<img width="595" height="424" alt="image" src="https://github.com/user-attachments/assets/b66eb436-bd15-48c3-b079-36a095e3b923" />

# Diamond clarity and prices

```python 
#How does clarity affect price?
colorvsprice = pd.crosstab(df['color'], df['priceclass'], normalize='index')*100
colorvsprice.style.format('{:.0f}')
n_1 = len(df[df['color']=='D'])
n_2 = len(df[df['color']=='E'])
n_3 = len(df[df['color']=='F'])
n_4 = len(df[df['color']=='G'])
n_5 = len(df[df['color']=='H'])
n_6 = len(df[df['color']=='I'])
n_7 = len(df[df['color']=='J'])
colorvsprice.index = [f'D, n={n_1}', f'E, n={n_2}', f'F, n={n_3}', f'G, n={n_4}', f'H, n={n_5}', f'I, n={n_6}', f'J, n={n_7}']
colorvsprice.style.format('{:.1f}%')
#We can see the prices of color D to F is lower than the prices of color G to J.
```

<img width="944" height="246" alt="image" src="https://github.com/user-attachments/assets/ef0a78e2-86da-4e33-aa65-3171b3acdb4c" />

```python
#Make a barchart from priceclass and diamond color.
colorvsprice.plot(kind='barh', stacked=True, cmap='magma')
plt.xlim([0, 100])
plt.xticks([0, 10, 20, 30, 40, 50, 60, 70, 80, 90, 100])
plt.grid(axis='x')
plt.legend(loc=[0, -0.50])
plt.xlabel('% of color grade')
plt.show()
```

<img width="644" height="572" alt="image" src="https://github.com/user-attachments/assets/976d9cf1-28ee-4641-81bf-5153383fbf5a" />
