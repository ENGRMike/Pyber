

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
```


```python
#read csv files and save them as a dataframe
Rdata_df = pd.read_csv('ride_data.csv')
Cdata_df = pd.read_csv('city_data.csv')

#Calculate the average fare per and total rides per city
Avg_Fare = pd.DataFrame(Rdata_df.groupby('city')['fare'].mean())
Avg_Fare = Avg_Fare.reset_index()
Total_Rides = pd.DataFrame(Rdata_df.groupby('city')['ride_id'].count())
Total_Rides = Total_Rides.reset_index()
Total_Rides = Total_Rides.rename(columns={'ride_id':'total number of rides'})
Drivers = pd.DataFrame(Cdata_df.groupby('city')['driver_count'].sum())
Drivers = Drivers.reset_index()
Type = Cdata_df[['city','type']].drop_duplicates()
Type = pd.DataFrame(Cdata_df.drop_duplicates())
Type = Type.reset_index()
Type = Type[['city','type']]

#Merge the data into one dataframe
Pyber_Data1 = pd.merge(Drivers,Avg_Fare, how = 'inner', on = 'city')
Pyber_Data2 = pd.merge(Pyber_Data1, Total_Rides, how = 'inner', on = 'city')
Pyber_Data3 = pd.merge(Pyber_Data2, Type, how='inner',on='city')
Pyber_Data = Pyber_Data3.rename(columns = {'fare':'average fare','driver_count':'driver count'})
Pyber_Data_format = Pyber_Data
Pyber_Data_format['average fare'] = Pyber_Data_format['average fare'].map("${0:,.2f}".format)
Pyber_Data_format = Pyber_Data_format[['city','total number of rides','average fare','driver count','type']]
Pyber_Data_format.head(5)
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>city</th>
      <th>total number of rides</th>
      <th>average fare</th>
      <th>driver count</th>
      <th>type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Alvarezhaven</td>
      <td>31</td>
      <td>$23.93</td>
      <td>21</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Alyssaberg</td>
      <td>26</td>
      <td>$20.61</td>
      <td>67</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Anitamouth</td>
      <td>9</td>
      <td>$37.32</td>
      <td>16</td>
      <td>Suburban</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Antoniomouth</td>
      <td>22</td>
      <td>$23.62</td>
      <td>21</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Aprilchester</td>
      <td>19</td>
      <td>$21.98</td>
      <td>49</td>
      <td>Urban</td>
    </tr>
  </tbody>
</table>
</div>




```python
fig, ax = plt.subplots()
groups = Pyber_Data3.groupby('type')
colors = ['Gold','lightskyblue','lightcoral']
for i, (name, group) in enumerate(groups):
    group.plot(kind = 'scatter', x='total number of rides',y='fare', ylim=((15,55)), xlim=((0,50)), s=Pyber_Data3['driver_count'].values*2, label=name, ax=ax, color=colors[i], edgecolor='black', alpha=0.65)
lgd = ax.legend(numpoints=1)
ax.set_ylabel('Average Fare ($)')
ax.set_xlabel('Total Number of Rides (Per City)')
ax.set_title('Pyber Ride Sharing Data (2016)')
ax.grid()
for handle in lgd.legendHandles:
    handle.set_sizes([100.0])
plt.show()
```


![png](output_2_0.png)



```python
Rides_df = pd.merge(Rdata_df,Cdata_df,how ='right', on='city')
Fare_Type = pd.DataFrame(Rides_df.groupby('type')['fare'].sum())
Fare_Type = Fare_Type.reset_index()

Cutout1 = [0.1,0.1,0]
plt.pie(Fare_Type['fare'], explode=Cutout1, labels = Fare_Type['type'], colors = colors, autopct="%1.1f%%", shadow=True, startangle=90)
plt.title('% of Total Fares by City Type')
plt.tight_layout()
plt.show()
```


![png](output_3_0.png)



```python
Rides_Type = pd.DataFrame(Rides_df.groupby('type')['fare'].count())
Rides_Type = Rides_Type.reset_index()
Rides_Type = Rides_Type.rename(columns={'fare':'total rides'})

Cutout2 = [0.1,0.1,0]
plt.pie(Rides_Type['total rides'], explode=Cutout2, labels = Rides_Type['type'], colors = colors, autopct="%1.1f%%", shadow=True, startangle=90)
plt.title('% of Total Rides by City Type')
plt.tight_layout()
plt.show()
```


![png](output_4_0.png)



```python
Driver_Count = pd.DataFrame(Cdata_df.groupby('type')['driver_count'].sum())
Driver_Count= Driver_Count.reset_index()
Cutout2 = [0.1,0.1,0]
plt.pie(Driver_Count['driver_count'], explode=Cutout2, labels = Driver_Count['type'], colors = colors, autopct="%1.1f%%", shadow=True, startangle=130)
plt.title('% of Total Drivers by City Type')
plt.tight_layout()
plt.show()
```


![png](output_5_0.png)

