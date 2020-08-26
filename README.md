```python
#!pip install pytrends
from pytrends.request import TrendReq
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import matplotlib
from IPython.display import Image
```

<img src="https://1.bp.blogspot.com/-J0sQ6HQMWfs/X0O_WbrgNYI/AAAAAAAABmY/HkbazKkxfhg2JkHYsPtwpOLq5AV89WxjgCLcBGAsYHQ/s640/d1.png" width="50%">

<img src="https://1.bp.blogspot.com/-IAa8dbnmARc/X0O_X6MOsrI/AAAAAAAABmc/LTt93FOCAJIZYb_dnE_mW4oDOzRNt2tHQCLcBGAsYHQ/s640/d2.png" width=50%>

# **ISS Live feed**
### **International Space Station Current Location**
http://open-notify.org/Open-Notify-API/ISS-Location-Now/

The ISS programme is a joint project between five participating space agencies: NASA (United States), Roscosmos (Russia), JAXA (Japan), ESA (Europe), and CSA (Canada). The ownership and use of the space station is established by intergovernmental treaties and agreements.

The ISS serves as a microgravity and space environment research laboratory in which crew members conduct experiments in biology, physics, astronomy, and other fields.

This is all from Wikipedia.

### **Who are the astronauts on board right now?**

GO TO: http://open-notify.org/


```python
# Who is in space right now?
import requests
r = requests.get(url='http://api.open-notify.org/astros.json')
t=r.json()
print(t)
print(t['number'])
print(t['people'][0]['name'])
print(t['people'][1]['name'])
print(t['people'][2]['name'])
```

    {'number': 3, 'people': [{'craft': 'ISS', 'name': 'Chris Cassidy'}, {'craft': 'ISS', 'name': 'Anatoly Ivanishin'}, {'craft': 'ISS', 'name': 'Ivan Vagner'}], 'message': 'success'}
    3
    Chris Cassidy
    Anatoly Ivanishin
    Ivan Vagner



```python
    r = requests.get(url='http://api.open-notify.org/astros.json')
    point=r.json()
    number=point['number']
    print(number)
    astro=[]
    for i in range(0,number):
        astro.append(point['people'][i]['name'])
    print(astro)
```

    3
    ['Chris Cassidy', 'Anatoly Ivanishin', 'Ivan Vagner']


## Where is the International Space Station right now?

<img src="https://1.bp.blogspot.com/-7VR9GlxYkxA/X0O_Zyrh1aI/AAAAAAAABmg/32e8uGZCkNEV5F4QU2BFcNJHC10k9D8kwCLcBGAsYHQ/s640/d3.png" width=60%>


```python
r = requests.get(url='http://api.open-notify.org/iss-now.json')
space_station_location = (r.json())
print(space_station_location)

space_station_location['iss_position']['latitude']
space_station_location['iss_position']['longitude']
space_station_location['timestamp']
```

    {'timestamp': 1598432463, 'message': 'success', 'iss_position': {'longitude': '163.7154', 'latitude': '-51.5850'}}





    1598432463




```python
# let's plot the ISS current location
# you will need to pip install Basemap - https://matplotlib.org/basemap/users/installing.html
#!sudo apt-get install libgeos-3.5.0
#!sudo apt-get install libgeos-dev
#!sudo pip install https://github.com/matplotlib/basemap/archive/master.zip
from mpl_toolkits.basemap import Basemap

# Set the dimension of the figure
plt.figure(figsize=(16, 8))

# Make the background map
m=Basemap(llcrnrlon=-180, llcrnrlat=-65,urcrnrlon=180,urcrnrlat=80)
m.drawmapboundary(fill_color='#A6CAE0', linewidth=0)
m.fillcontinents(color='grey', alpha=0.3)
m.drawcoastlines(linewidth=0.1, color="white")


m.scatter(float(space_station_location['iss_position']['longitude']), 
          float(space_station_location['iss_position']['latitude']), 
          s=500, alpha=0.4,color='blue')

 
plt.title('International Space Station Location' , fontsize=30) 
```




    Text(0.5, 1.0, 'International Space Station Location')




![png](output_9_1.png)


# **Collect data - try to let it run over**
We know that it orbits 15.5 perday, so let it run at least 2 hours to collect enough data to see it go around the earth once.


```python
record_data = True
if record_data == True:
    import datetime
    date_to_print = datetime.datetime.now().strftime("%Y%m%d%H%M%S")

    import time
    starttime=time.time()


    space_station_data = []
    while True: 
        r = requests.get(url='http://api.open-notify.org/iss-now.json')
        space_station_location = (r.json())
        print(space_station_location)

        space_station_data.append([space_station_location['timestamp'],
                                space_station_location['iss_position']['latitude'],
                                space_station_location['iss_position']['longitude']
                                ])

        # dump copy to file
        tmp_space_station_data_df = pd.DataFrame(space_station_data, columns=['timestamp','latitude', 'longitude',])
        tmp_space_station_data_df.to_csv('ISS_location_' + date_to_print + '.csv', index=None)
        
        # safety break
        if len(space_station_data) > 600:
            break
            
        # let it sleep 60 seconds
        # https://stackoverflow.com/questions/474528/what-is-the-best-way-to-repeatedly-execute-a-function-every-x-seconds-in-python
        time.sleep(60.0 - ((time.time() - starttime) % 60.0))
```

    {'timestamp': 1598278430, 'iss_position': {'longitude': '-147.3252', 'latitude': '31.8861'}, 'message': 'success'}
    {'timestamp': 1598278490, 'iss_position': {'longitude': '-144.1312', 'latitude': '34.4923'}, 'message': 'success'}
    {'timestamp': 1598278550, 'iss_position': {'longitude': '-140.7481', 'latitude': '36.9754'}, 'message': 'success'}
    {'timestamp': 1598278610, 'iss_position': {'longitude': '-137.0943', 'latitude': '39.3597'}, 'message': 'success'}
    {'timestamp': 1598278670, 'iss_position': {'longitude': '-133.1724', 'latitude': '41.6044'}, 'message': 'success'}
    {'timestamp': 1598278730, 'iss_position': {'longitude': '-128.9606', 'latitude': '43.6874'}, 'message': 'success'}
    {'timestamp': 1598278790, 'iss_position': {'longitude': '-124.4422', 'latitude': '45.5845'}, 'message': 'success'}
    {'timestamp': 1598278850, 'iss_position': {'longitude': '-119.6505', 'latitude': '47.2565'}, 'message': 'success'}
    {'timestamp': 1598278910, 'iss_position': {'longitude': '-114.5098', 'latitude': '48.7045'}, 'message': 'success'}
    {'timestamp': 1598278970, 'iss_position': {'longitude': '-109.0806', 'latitude': '49.8865'}, 'message': 'success'}
    {'timestamp': 1598279030, 'iss_position': {'longitude': '-103.4021', 'latitude': '50.7779'}, 'message': 'success'}
    {'timestamp': 1598279090, 'iss_position': {'longitude': '-97.5833', 'latitude': '51.3545'}, 'message': 'success'}
    {'timestamp': 1598279150, 'iss_position': {'longitude': '-91.6029', 'latitude': '51.6116'}, 'message': 'success'}
    {'timestamp': 1598279210, 'iss_position': {'longitude': '-85.5984', 'latitude': '51.5362'}, 'message': 'success'}
    {'timestamp': 1598279270, 'iss_position': {'longitude': '-79.6595', 'latitude': '51.1304'}, 'message': 'success'}
    {'timestamp': 1598279330, 'iss_position': {'longitude': '-73.9179', 'latitude': '50.4120'}, 'message': 'success'}
    {'timestamp': 1598279390, 'iss_position': {'longitude': '-68.3463', 'latitude': '49.3863'}, 'message': 'success'}
    {'timestamp': 1598279450, 'iss_position': {'longitude': '-63.0445', 'latitude': '48.0809'}, 'message': 'success'}
    {'timestamp': 1598279510, 'iss_position': {'longitude': '-58.0434', 'latitude': '46.5217'}, 'message': 'success'}
    {'timestamp': 1598279570, 'iss_position': {'longitude': '-53.3562', 'latitude': '44.7359'}, 'message': 'success'}
    {'timestamp': 1598279630, 'iss_position': {'longitude': '-49.0161', 'latitude': '42.7675'}, 'message': 'success'}
    {'timestamp': 1598279690, 'iss_position': {'longitude': '-44.9385', 'latitude': '40.6090'}, 'message': 'success'}
    {'timestamp': 1598279750, 'iss_position': {'longitude': '-41.1413', 'latitude': '38.2991'}, 'message': 'success'}
    {'timestamp': 1598279810, 'iss_position': {'longitude': '-37.6011', 'latitude': '35.8587'}, 'message': 'success'}
    {'timestamp': 1598279870, 'iss_position': {'longitude': '-34.2929', 'latitude': '33.3064'}, 'message': 'success'}
    {'timestamp': 1598279930, 'iss_position': {'longitude': '-31.2161', 'latitude': '30.6808'}, 'message': 'success'}
    {'timestamp': 1598279990, 'iss_position': {'longitude': '-28.2949', 'latitude': '27.9521'}, 'message': 'success'}
    {'timestamp': 1598280050, 'iss_position': {'longitude': '-25.5325', 'latitude': '25.1544'}, 'message': 'success'}
    {'timestamp': 1598280110, 'iss_position': {'longitude': '-22.9069', 'latitude': '22.2984'}, 'message': 'success'}
    {'timestamp': 1598280170, 'iss_position': {'longitude': '-20.4186', 'latitude': '19.4186'}, 'message': 'success'}
    {'timestamp': 1598280230, 'iss_position': {'longitude': '-18.0070', 'latitude': '16.4746'}, 'message': 'success'}
    {'timestamp': 1598280290, 'iss_position': {'longitude': '-15.6761', 'latitude': '13.4985'}, 'message': 'success'}
    {'timestamp': 1598280350, 'iss_position': {'longitude': '-13.4094', 'latitude': '10.4968'}, 'message': 'success'}
    {'timestamp': 1598280410, 'iss_position': {'longitude': '-11.1917', 'latitude': '7.4761'}, 'message': 'success'}
    {'timestamp': 1598280470, 'iss_position': {'longitude': '-9.0264', 'latitude': '4.4673'}, 'message': 'success'}
    {'timestamp': 1598280530, 'iss_position': {'longitude': '-6.8632', 'latitude': '1.4258'}, 'message': 'success'}
    {'timestamp': 1598280590, 'iss_position': {'longitude': '-4.7068', 'latitude': '-1.6178'}, 'message': 'success'}
    {'timestamp': 1598280650, 'iss_position': {'longitude': '-2.5433', 'latitude': '-4.6583'}, 'message': 'success'}
    {'timestamp': 1598280710, 'iss_position': {'longitude': '-0.3776', 'latitude': '-7.6647'}, 'message': 'success'}
    {'timestamp': 1598280770, 'iss_position': {'longitude': '1.8407', 'latitude': '-10.6820'}, 'message': 'success'}



    ---------------------------------------------------------------------------

    KeyboardInterrupt                         Traceback (most recent call last)

    <ipython-input-32-d7ba924d2496> in <module>()
         29         # let it sleep 60 seconds
         30         # https://stackoverflow.com/questions/474528/what-is-the-best-way-to-repeatedly-execute-a-function-every-x-seconds-in-python
    ---> 31         time.sleep(60.0 - ((time.time() - starttime) % 60.0))
    

    KeyboardInterrupt: 


# **Visualize the historical data**


```python
# load historical data
iss_flight_record = pd.read_csv('ISS_location_20200824141350.csv')
# translate timestamp into readable
from datetime import datetime
date_time = [datetime.fromtimestamp(dt) for dt in iss_flight_record['timestamp']] 

# add teh date_time to a new column in our data frame iss_flight_record
iss_flight_record['date'] = date_time

# add an plot size from oldest to newest
iss_flight_record['index'] = range(1,len(iss_flight_record)+1)
 
iss_flight_record.head()
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
      <th>timestamp</th>
      <th>latitude</th>
      <th>longitude</th>
      <th>date</th>
      <th>index</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1598278430</td>
      <td>31.8861</td>
      <td>-147.3252</td>
      <td>2020-08-24 14:13:50</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1598278490</td>
      <td>34.4923</td>
      <td>-144.1312</td>
      <td>2020-08-24 14:14:50</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1598278550</td>
      <td>36.9754</td>
      <td>-140.7481</td>
      <td>2020-08-24 14:15:50</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1598278610</td>
      <td>39.3597</td>
      <td>-137.0943</td>
      <td>2020-08-24 14:16:50</td>
      <td>4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1598278670</td>
      <td>41.6044</td>
      <td>-133.1724</td>
      <td>2020-08-24 14:17:50</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>




```python
# you will need to pip install Basemap - https://matplotlib.org/basemap/users/installing.html
from mpl_toolkits.basemap import Basemap

# Set the dimension of the figure
plt.figure(figsize=(16, 8))

# Make the background map
m=Basemap(llcrnrlon=-180, llcrnrlat=-65,urcrnrlon=180,urcrnrlat=80)
m.drawmapboundary(fill_color='#A6CAE0', linewidth=0)
m.fillcontinents(color='grey', alpha=0.3)
m.drawcoastlines(linewidth=0.1, color="white")

 
 
m.scatter(iss_flight_record['longitude'], 
          iss_flight_record['latitude'], 
          s=iss_flight_record['index'] , alpha=0.4,color='blue')

 
plt.title('International Space Station Location' , fontsize=30)
```




    Text(0.5, 1.0, 'International Space Station Location')




![png](output_14_1.png)

