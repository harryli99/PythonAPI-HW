

```
import matplotlib.pyplot as plt
import requests as req
import pandas as pd
from citipy import citipy
import numpy as np
```


```
#Building API URL
api_key = "4331a4f5738542dd5a6354671e2eb977"
url = "http://api.openweathermap.org/data/2.5/weather?"
units = "imperial"

# Build partial query URL
query_url = url + "appid=" + api_key + "&units=" + units + "&q="
```


```
#Creating a list of cities from random coordinates
cities  = []
latsList = np.random.uniform(-90, 90,1150) # the size of the sample is adjusted until we have approximately 500 unique cities
lngsList = np.random.uniform(-180, 180, 1150)
for i in range(len(latsList)):
        name = citipy.nearest_city(latsList[i], lngsList[i])
        cities.append(name.city_name)
len(np.unique(cities)) # checks how many unique cities generated
```




    508




```
#Create a list of unique cities
unique_cities = np.unique(cities).tolist()
#remove the extra cities at random to get 500 unique cities
for i in range(8):
    unique_cities.pop(np.random.randint(len(unique_cities)))
```


```
weather_data = []
# Loop through the list of cities and perform a request for data on each
for city in unique_cities:
    response = req.get(query_url + city).json()
    print(response)
    weather_data.append(response)
```


```
#Retrieve the data from API for each city
lat_data = [data.get("coord").get("lat") for data in weather_data] 
lng_data = [data.get("coord").get("lon") for data in weather_data] 
temp_data = [data.get("main").get("temp") for data in weather_data]
humid_data = [data.get("main").get("humidity") for data in weather_data] 
cloudy_data = [data.get("clouds").get("all") for data in weather_data] 
wind_data = [data.get("wind").get("speed") for data in weather_data] 
```


    ---------------------------------------------------------------------------

    AttributeError                            Traceback (most recent call last)

    <ipython-input-90-54cb8c00a169> in <module>()
          1 #Retrieve the data from API for each city
    ----> 2 lat_data = [data.get("coord").get("lat") for data in weather_data]
          3 lng_data = [data.get("coord").get("lon") for data in weather_data]
          4 temp_data = [data.get("main").get("temp") for data in weather_data]
          5 humid_data = [data.get("main").get("humidity") for data in weather_data]
    

    <ipython-input-90-54cb8c00a169> in <listcomp>(.0)
          1 #Retrieve the data from API for each city
    ----> 2 lat_data = [data.get("coord").get("lat") for data in weather_data]
          3 lng_data = [data.get("coord").get("lon") for data in weather_data]
          4 temp_data = [data.get("main").get("temp") for data in weather_data]
          5 humid_data = [data.get("main").get("humidity") for data in weather_data]
    

    AttributeError: 'NoneType' object has no attribute 'get'



```
#Building DataFrame
weather_df = {"Cities": unique_cities, "Temperature(F)": temp_data, "Latitude": lat_data, "Longitude", "Humidity(%)": lng_data,
               "Cloudiness(%)": cloudy_data, "Wind Speed(mph)": wind_data}
weather_df = pd.DataFrame(weather_data)
weather_df.to_csv('weatherdataframe.csv')
weather_df.head()
```


```
# Build a scatter plot for each data type
plt.scatter(weather_df["Latitude"], weather_df["Temperature(F)"], marker="o")

# Incorporate the other graph properties
plt.title("Latitude Vs Temperature")
plt.ylabel("Temperature(F)")
plt.xlabel("Latitude")
plt.grid(True)
plt.show()
```


```
plt.scatter(weather_df["Latitude"], weather_df["Humidity(%)"], marker="o")

plt.title("Latitude Vs Humidity")
plt.ylabel("Humidity(%)")
plt.xlabel("Latitude")
plt.grid(True)
plt.show()
```


```
plt.scatter(weather_df["Latitude"], weather_df["Cloudiness(%)"], marker="o")

plt.title("Latitude Vs Cloudiness")
plt.ylabel("Cloudiness(%)")
plt.xlabel("Latitude")
plt.grid(True)
plt.show()
```


```
plt.scatter(weather_df["Latitude"], weather_df["Wind Speed(mph)"], marker="o")

plt.title("Latitude Vs Wind Speed")
plt.ylabel("Wind Speed(mph)")
plt.xlabel("Latitude")
plt.grid(True)
plt.show()
```
