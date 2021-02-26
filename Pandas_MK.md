import pandas as pd
import os as os

os.chdir("E:\python\PROJECTS\S_LYTICS")
files = [file for file in os.listdir()]
all_data = pd.DataFrame()
for file in files:
    df = pd.read_csv("./"+file)
    all_data = pd.concat([all_data,df])
    
df = all_data
df.head(5)



```python

```


```python

```

df["Month"] = df["Order Date"].str[0:2]
df = df.dropna(how = "all")
df = df[df["Month"].str[0:2] != "Or"]
df.head()

df["Month"].describe()
df["Month"] = df["Month"].astype("int")
df["Month"].describe()
df["Price Each"] = pd.to_numeric(df["Price Each"])
df["Quantity Ordered"] = pd.to_numeric(df["Quantity Ordered"])
df["Price Each"].describe()

import numpy as np
new_data = np.random.randint(1,12, size = 185949)
new_data = pd.DataFrame(new_data,columns = ["Month2"])
df["Sales Month"] = new_data
pd.options.display.float_format = "{:,.0f}".format
df.groupby("Sales Month").sum()

df

def get_city(address):
    return address.split(",")[1]
def get_state(address):
    return address.split(",")[2].split(" ")[1]

df["City"] = df["Purchase Address"].apply(lambda x: get_city(x) + " (" + get_state(x) + ") ")
df.head()

results = df.groupby("City").sum()
results

import matplotlib.pyplot as plt

cities = [City for City, df in df.groupby("City")]
plt.bar(cities, results["Price Each"])
plt.xticks(cities, rotation = "vertical", size = 10)
plt.xlabel("cities")
plt.ylabel("Price")
plt.show

df["Order Date"] = pd.to_datetime(df["Order Date"])
df["Order Date"]

df["Hour"] = df["Order Date"].dt.hour
df["Minute"] = df["Order Date"].dt.minute
df

hours = [Hour for Hour, df in df.groupby("Hour")]
minutes = [Minute for Minute, df in df.groupby("Minute")]
results = df.groupby("Hour").count()

import matplotlib.pyplot as plt

plt.plot(hours, results["Price Each"])
plt.xticks(hours,rotation = "horizontal", size = 10)
plt.xlabel("hours")
plt.ylabel("Prices")
plt.grid()
plt.show()

#### What Sold were mostly sold together?

df2 = df[df["Order ID"].duplicated(keep = False)]
df["Grouped"] = df.groupby("Order ID")["Product"].transform(lambda x: ",".join(x))
df.head(20)

df3 = df[df["Order ID"].duplicated(keep = False)]
df3.head(10)
df3["Grouped"] = df3.groupby("Order ID")["Product"].transform(lambda x: ",".join(x))
df3.head(20)

results = df3.groupby("Grouped").agg({
    "Price Each" : "count"
})
results = results.sort_values("Price Each", ascending = False)
results

#### In Different Combinations

from itertools import combinations
from collections import Counter

count= Counter() 
for row in df["Grouped"]:
    row_list = row.split(",")
    count.update(Counter(combinations(row_list,2)))
for key, value in count.most_common(20):
    print(key, value)

