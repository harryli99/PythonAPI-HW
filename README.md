Python API HW

- In this homework we use the openweathermap API to find the relationshiop between latitude and temperature, windspeed, cloudiness, and humidity
- 3 observations noticed include:
1. Temperature decreases as cities move away from the equator
2. Most cities have a humitity level between 60% to 100%
3. Most cities have a wind speed that is between 0 to 15 mph

```python
import matplotlib.pyplot as plt
import requests as req
import pandas as pd
from citipy import citipy
import numpy as np
```


```python
#Building API URL
api_key = "4331a4f5738542dd5a6354671e2eb977"
url = "http://api.openweathermap.org/data/2.5/weather?"
units = "imperial"

# Build partial query URL
query_url = url + "appid=" + api_key + "&units=" + units + "&q="
```


```python
#Creating a list of cities from random coordinates
cities  = []
latsList = np.random.uniform(-90, 90,1500) # the size of the sample is adjusted until we have approximately 500 unique cities
lngsList = np.random.uniform(-180, 180, 1500)
for i in range(len(latsList)):
        name = citipy.nearest_city(latsList[i], lngsList[i])
        cities.append(name.city_name)
len(np.unique(cities)) # checks how many unique cities generated
```


```python
#Create a list of unique cities
unique_cities = np.unique(cities).tolist()
```


```python
weather_data = []
count = 1
# Loop through the list of cities and perform a request for data on each
for city in unique_cities:
    response = req.get(query_url + city).json()
    print("Processing Record", count, "of", len(unique_cities),"|", city+"\n"+query_url+city) #Prints the request log
    count += 1
    weather_data.append(response)
```
#Just going to list the first 10 log outputs
Processing Record 1 of 585 | abu kamal
http://api.openweathermap.org/data/2.5/weather?appid=4331a4f5738542dd5a6354671e2eb977&units=imperial&q=abu kamal
Processing Record 2 of 585 | acapulco
http://api.openweathermap.org/data/2.5/weather?appid=4331a4f5738542dd5a6354671e2eb977&units=imperial&q=acapulco
Processing Record 3 of 585 | aden
http://api.openweathermap.org/data/2.5/weather?appid=4331a4f5738542dd5a6354671e2eb977&units=imperial&q=aden
Processing Record 4 of 585 | adrar
http://api.openweathermap.org/data/2.5/weather?appid=4331a4f5738542dd5a6354671e2eb977&units=imperial&q=adrar
Processing Record 5 of 585 | ahipara
http://api.openweathermap.org/data/2.5/weather?appid=4331a4f5738542dd5a6354671e2eb977&units=imperial&q=ahipara
Processing Record 6 of 585 | ainazi
http://api.openweathermap.org/data/2.5/weather?appid=4331a4f5738542dd5a6354671e2eb977&units=imperial&q=ainazi
Processing Record 7 of 585 | airai
http://api.openweathermap.org/data/2.5/weather?appid=4331a4f5738542dd5a6354671e2eb977&units=imperial&q=airai
Processing Record 8 of 585 | aklavik
http://api.openweathermap.org/data/2.5/weather?appid=4331a4f5738542dd5a6354671e2eb977&units=imperial&q=aklavik
Processing Record 9 of 585 | aksarayskiy
http://api.openweathermap.org/data/2.5/weather?appid=4331a4f5738542dd5a6354671e2eb977&units=imperial&q=aksarayskiy
Processing Record 10 of 585 | aksu
http://api.openweathermap.org/data/2.5/weather?appid=4331a4f5738542dd5a6354671e2eb977&units=imperial&q=aksu

```python
#Create lists of datasets
city_name = []
lat_data = []
lng_data = []
temp_data = []
cloudy_data = []
humid_data = []
wind_data = []
#Data Cleaning: Excluding entries that don't have data
for data in weather_data:
    try:
        city_name.append(data.get("name"))
        lat_data.append(data.get("coord").get("lat"))
        lng_data.append(data.get("coord").get("lon"))
        temp_data.append(data.get("main").get("temp"))
        cloudy_data.append(data.get("clouds").get("all"))
        humid_data.append(data.get("main").get("humidity"))
        wind_data.append(data.get("wind").get("speed"))
    except:
         pass
city_name = [x for x in city_name if x is not None]
```


```python
=======

```
#Retrieve the data from API for each city
lat_data = [data.get("coord").get("lat") for data in weather_data] 
lng_data = [data.get("coord").get("lon") for data in weather_data] 
temp_data = [data.get("main").get("temp") for data in weather_data]
humid_data = [data.get("main").get("humidity") for data in weather_data] 
cloudy_data = [data.get("clouds").get("all") for data in weather_data] 
wind_data = [data.get("wind").get("speed") for data in weather_data] 
```

```
>>>>>>> 7500babf91c799789e3188392de08852520aa1d2
#Building DataFrame
weather_dataset = {"Cities": city_name, "Temperature(F)": temp_data, "Latitude": lat_data, "Longitude":lng_data, 
              "Humidity(%)": humid_data, "Cloudiness(%)": cloudy_data, "Wind Speed(mph)": wind_data}
weather_df = pd.DataFrame(weather_dataset)
weather_df = weather_df[["Cities","Latitude", "Longitude", "Temperature(F)", "Humidity(%)","Cloudiness(%)","Wind Speed(mph)"]]
weather_df.set_index("Cities", inplace= True)
weather_df.to_csv('weatherdataframe.csv') 
weather_df.head()
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
      <th>Latitude</th>
      <th>Longitude</th>
      <th>Temperature(F)</th>
      <th>Humidity(%)</th>
      <th>Cloudiness(%)</th>
      <th>Wind Speed(mph)</th>
    </tr>
    <tr>
      <th>Cities</th>
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
      <th>Abu Dhabi</th>
      <td>24.47</td>
      <td>54.37</td>
      <td>65.26</td>
      <td>93</td>
      <td>75</td>
      <td>2.24</td>
    </tr>
    <tr>
      <th>Acari</th>
      <td>-6.44</td>
      <td>-36.64</td>
      <td>70.17</td>
      <td>96</td>
      <td>20</td>
      <td>5.99</td>
    </tr>
    <tr>
      <th>Adrar</th>
      <td>27.87</td>
      <td>-0.29</td>
      <td>39.20</td>
      <td>51</td>
      <td>0</td>
      <td>10.29</td>
    </tr>
    <tr>
      <th>Adzope</th>
      <td>6.11</td>
      <td>-3.86</td>
      <td>59.73</td>
      <td>84</td>
      <td>0</td>
      <td>2.75</td>
    </tr>
    <tr>
      <th>Ahipara</th>
      <td>-35.17</td>
      <td>173.16</td>
      <td>63.78</td>
      <td>100</td>
      <td>92</td>
      <td>12.66</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Build a scatter plot for each data type
plt.scatter(weather_df["Latitude"], weather_df["Temperature(F)"], marker="o")

# Incorporate the other graph properties
plt.title("Latitude Vs Temperature")
plt.ylabel("Temperature(F)")
plt.xlabel("Latitude")
plt.grid(True)
plt.savefig("LatvsTemp.png")
plt.show()
```


![png](output_7_0.png)



```python
plt.scatter(weather_df["Latitude"], weather_df["Humidity(%)"], marker="o")

plt.title("Latitude Vs Humidity")
plt.ylabel("Humidity(%)")
plt.xlabel("Latitude")
plt.grid(True)
plt.savefig("LatvsHumid.png")
plt.show()
```


![png](output_8_0.png)



```python
plt.scatter(weather_df["Latitude"], weather_df["Cloudiness(%)"], marker="o")

plt.title("Latitude Vs Cloudiness")
plt.ylabel("Cloudiness(%)")
plt.xlabel("Latitude")
plt.grid(True)
plt.savefig("LatvsCloud.png")
plt.show()
```


![png](output_9_0.png)



```python
plt.scatter(weather_df["Latitude"], weather_df["Wind Speed(mph)"], marker="o")

plt.title("Latitude Vs Wind Speed")
plt.ylabel("Wind Speed(mph)")
plt.xlabel("Latitude")
plt.grid(True)
plt.savefig("LatvsWind.png")
plt.show()
```


![png](output_10_0.png)

