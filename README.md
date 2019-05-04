# WeatherPy

### Analysis

* Here is an brief view of the sampled cities, to provide an insight to the sample size on a global scale.  The data was sampled one final time on 5/3 at around 9:30 PM Eastern Time.  
![Latitude Vs Longitude of Sampled Cities](output_data/LatVsLong.png?raw=true "Latitude vs Max Temperature (F)")


* It is unsurprising but very visible in the data shown below,  that as you get closer to the equator (0 Latitude) that the max temp goes up and as you get further away, north or south that the max temperature goes down.  
![Latitude Vs Max Temperature (F)](output_data/LatitudeVsMaxTemp.png?raw=true "Latitude vs Max Temperature (F)")

* As seen in the below image there is very little correlation between Latitude and % Humidity.  
![Latitude Vs % Humidity](output_data/LatitudeVsHumidity.png?raw=true "Latitude vs % Humidity")

* As visible in the image here there is also very little correlation between Latitude and % Cloudiness.  In fact the % Cloudiness viewed in relation to the Latitude seems to be completely random.  
![Latitude Vs % Cloudiness](output_data/LatitudeVsCloudiness.png?raw=true "Latitude vs % Cloudiness")

* There is nearly no correlation between Latitude and Wind Speed shown in this image.  However it can be concluded that most of the sampled cities around or less than 10 MPH.  
![Latitude Vs Wind Speed (MPH)](output_data/LatitudeVsWindSpeed.png?raw=true "Latitude vs Wind Speed (MPH)")

* In processing all of the above images I became curious.  I wanted to go one step further and see if there was any correlation between Longitude and any of these datasets.  I replaced Latitude with Longitude for all of the above charts and plotted them the same way. Here is a comparison between each Latitude chart and each Longitude chart.

* Overall it can be observed that generally Longitude does not correlate to any of these categories.  
    * This is not a surprising conclusion due to the rotation of the earth and the progression of a day.  Latitude has much more of an impact on Max Temperature than Longitude would.


These two charts are Latitude (on the Left) and Longitude (on the Right) versus the Max Temperature of Sampled Cities in Degress Farenheit.  
![Latitude Vs Max Temperature (F)](output_data/LatitudeVsMaxTemp.png?raw=true "Latitude vs Max Temperature (F)")
![Longitude Vs Max Temperature (F)](output_data/LongitudeVsMaxTemp.png?raw=true "Latitude vs Max Temperature (F)")  


These two charts are Latitude (on the Left) and Longitude (on the Right) versus the Percent Humidity of Sampled Cities.  
![Latitude Vs % Humidity](output_data/LatitudeVsHumidity.png?raw=true "Latitude vs % Humidity")
![Longitude Vs % Humidity](output_data/LongitudeVsHumidity.png?raw=true "Latitude vs % Humidity")

These two charts are Latitude (on the Left) and Longitude (on the Right) versus the Percent Cloudiness of Sampled Cities.  
![Latitude Vs % Cloudiness](output_data/LatitudeVsCloudiness.png?raw=true "Latitude vs % Cloudiness")
![Longitude Vs % Cloudiness](output_data/LongitudeVsCloudiness.png?raw=true "Latitude vs % Cloudiness")

These two charts are Latitude (on the Left) and Longitude (on the Right) versus the Wind Speed of Sampled Cities in Miles Per Hour.  
![Latitude Vs Wind Speed (MPH)](output_data/LatitudeVsWindSpeed.png?raw=true "Latitude vs Wind Speed (MPH)")
![Longitude Vs Wind Speed (MPH)](output_data/LongitudeVsWindSpeed.png?raw=true "Latitude vs Wind Speed (MPH)")


# Time for An insight into the Code.


#### Here we import the dependcies and do some basic set up.
```
# Dependencies and Setup
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np
import requests
import time
import random

# Import API key
from api_keys import api_key

# Incorporated citipy to determine city based on latitude and longitude
from citipy import citipy

# Output File (CSV)
output_data_file = "output_data/cities.csv"

# Range of latitudes and longitudes
lat_range = (-90, 90)
lng_range = (-180, 180)
```

#### This block of code generates a list of cities to run through the openweatherapi to get the current weather and then prints the amount of cities
```
# List for holding lat_lngs and cities
lat_lngs = []
cities = []

# Create a set of random lat and lng combinations
lats = np.random.uniform(low=-90.000, high=90.000, size=1500)
lngs = np.random.uniform(low=-180.000, high=180.000, size=1500)
lat_lngs = zip(lats, lngs)

# Identify nearest city for each lat, lng combination
for lat_lng in lat_lngs:
    city = citipy.nearest_city(lat_lng[0], lat_lng[1]).city_name

    # If the city is unique, then add it to a our cities list
    if city not in cities:
        cities.append(city)

# Print the city count to confirm sufficient count
len(cities)
```
```
629
```
#### Defined a function to complete the API call, not necessaray but made things easier.
```
def api_call(x,y):
    return requests.get(f'http://api.openweathermap.org/data/2.5/weather?units=Imperial&APPID={x}&q={y}').json()
```

#### Set up columns for dataframe and then creates the dataframe with the columns.
```
columns = ("City","Cloudiness","Country","Date","Humidity","Lat","Lng","Max Temp","Wind Speed")
weatherdata = pd.DataFrame(columns=columns)
```

#### This block of code is where the magic happens.
*  We create a few numbers to use as the dataframe index and output while processing
* For each city in the list of cities this runs the function defined previously and tries to process it by adding all of the required information to the dataframe.  
    * This block of code in the "try" statement then sleeps for a a random interval between 1 and 3 seconds.
    * If the city cannot be retreived with the openweatherapi it skips it and informs the user that it cannot be found.  
    * This then groups the data into sets of 50, so once the iterator (record) goes above 50 it increments the set and resets the iterator to 1.

```
print(f"Beinning Data Retreival")
print("-"*27)

record = 1
group = 1
index = 1

for city in cities:
    response = api_call(api_key,city)
    try:
        print(f"Processing Record {record} of Set {group} | {city}")
        weatherdata.set_value(index, "City", city)
        weatherdata.set_value(index, "Cloudiness", response['clouds']['all'])
        weatherdata.set_value(index, "Country", response['sys']['country'])
        weatherdata.set_value(index, "Date", response['dt'])
        weatherdata.set_value(index, "Humidity", response['main']['humidity'])
        weatherdata.set_value(index, "Lat", response['coord']['lat'])
        weatherdata.set_value(index, "Lng", response['coord']['lon'])
        weatherdata.set_value(index, "Max Temp", response['main']['temp_max'])
        weatherdata.set_value(index, "Wind Speed", response['wind']['speed'])
        record +=1
        index +=1
        time.sleep(random.randint(1,3))
    except:
        print("City not found. Skipping...")
        record +=1
    finally:
        if record > 50:
            record = 1
            group += 1

```
* Snippet of output from previous block of code.  To see entire output feel free to view the notebook file.  

```
Beinning Data Retreival
---------------------------
Processing Record 1 of Set 1 | rikitea

C:\Users\bryan\Miniconda3\lib\site-packages\ipykernel_launcher.py:12: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
  if sys.path[0] == '':
C:\Users\bryan\Miniconda3\lib\site-packages\ipykernel_launcher.py:13: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
  del sys.path[0]
C:\Users\bryan\Miniconda3\lib\site-packages\ipykernel_launcher.py:14: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead

C:\Users\bryan\Miniconda3\lib\site-packages\ipykernel_launcher.py:15: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
  from ipykernel import kernelapp as app
C:\Users\bryan\Miniconda3\lib\site-packages\ipykernel_launcher.py:16: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
  app.launch_new_instance()
C:\Users\bryan\Miniconda3\lib\site-packages\ipykernel_launcher.py:17: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
C:\Users\bryan\Miniconda3\lib\site-packages\ipykernel_launcher.py:18: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
C:\Users\bryan\Miniconda3\lib\site-packages\ipykernel_launcher.py:19: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead
C:\Users\bryan\Miniconda3\lib\site-packages\ipykernel_launcher.py:20: FutureWarning: set_value is deprecated and will be removed in a future release. Please use .at[] or .iat[] accessors instead

Processing Record 2 of Set 1 | yagodnoye
Processing Record 3 of Set 1 | tabiauea
City not found. Skipping...
Processing Record 4 of Set 1 | ondorhaan
City not found. Skipping...
Processing Record 5 of Set 1 | atuona
Processing Record 6 of Set 1 | taolanaro
City not found. Skipping...
Processing Record 7 of Set 1 | bambous virieux
Processing Record 8 of Set 1 | xuddur
Processing Record 9 of Set 1 | luoyang
Processing Record 10 of Set 1 | huilong
Processing Record 11 of Set 1 | ushuaia
Processing Record 12 of Set 1 | henties bay
Processing Record 13 of Set 1 | dikson
Processing Record 14 of Set 1 | rodrigues alves
Processing Record 15 of Set 1 | punta arenas
Processing Record 16 of Set 1 | albany
Processing Record 17 of Set 1 | kapaa
Processing Record 18 of Set 1 | baykit
Processing Record 19 of Set 1 | khatanga
Processing Record 20 of Set 1 | erdenet
Processing Record 21 of Set 1 | saint-francois
Processing Record 22 of Set 1 | barrow
Processing Record 23 of Set 1 | slonim
Processing Record 24 of Set 1 | rocha
Processing Record 25 of Set 1 | bredasdorp
Processing Record 26 of Set 1 | mar del plata
Processing Record 27 of Set 1 | hilo
Processing Record 28 of Set 1 | saldanha
Processing Record 29 of Set 1 | qaanaaq
Processing Record 30 of Set 1 | bathsheba
Processing Record 31 of Set 1 | ponta do sol
Processing Record 32 of Set 1 | coquimbo
Processing Record 33 of Set 1 | malwan
City not found. Skipping...
Processing Record 34 of Set 1 | palabuhanratu
City not found. Skipping...
Processing Record 35 of Set 1 | thompson
Processing Record 36 of Set 1 | cape town
Processing Record 37 of Set 1 | palatka
Processing Record 38 of Set 1 | kapoeta
City not found. Skipping...
Processing Record 39 of Set 1 | quelimane
Processing Record 40 of Set 1 | peace river
Processing Record 41 of Set 1 | victoria
Processing Record 42 of Set 1 | nabire
Processing Record 43 of Set 1 | katiola
Processing Record 44 of Set 1 | mataura
Processing Record 45 of Set 1 | new norfolk
Processing Record 46 of Set 1 | avarua
Processing Record 47 of Set 1 | hamilton
Processing Record 48 of Set 1 | salalah
Processing Record 49 of Set 1 | tokur
Processing Record 50 of Set 1 | carnarvon
Processing Record 1 of Set 2 | nemuro
Processing Record 2 of Set 2 | butaritari
Processing Record 3 of Set 2 | turukhansk
Processing Record 4 of Set 2 | cienfuegos
Processing Record 5 of Set 2 | comodoro rivadavia
Processing Record 6 of Set 2 | tuatapere
Processing Record 7 of Set 2 | chokurdakh
Processing Record 8 of Set 2 | saint-gaudens
Processing Record 9 of Set 2 | sao filipe
Processing Record 10 of Set 2 | saint-philippe
Processing Record 11 of Set 2 | codrington
Processing Record 12 of Set 2 | usinsk
Processing Record 13 of Set 2 | bundaberg
Processing Record 14 of Set 2 | talnakh
Processing Record 15 of Set 2 | port alfred
Processing Record 16 of Set 2 | bam
Processing Record 17 of Set 2 | samusu
City not found. Skipping...
Processing Record 18 of Set 2 | vila franca do campo
Processing Record 19 of Set 2 | kaitangata
Processing Record 20 of Set 2 | cabo san lucas
Processing Record 21 of Set 2 | williams lake
Processing Record 22 of Set 2 | lourdes
Processing Record 23 of Set 2 | puerto madryn
Processing Record 24 of Set 2 | mahibadhoo
Processing Record 25 of Set 2 | lichinga
Processing Record 26 of Set 2 | sechura
Processing Record 27 of Set 2 | tawau
Processing Record 28 of Set 2 | upernavik
Processing Record 29 of Set 2 | egvekinot
Processing Record 30 of Set 2 | cockburn town
```

#### This prints an overview of the dataframe to ensure that data has been properly added to it.
```
weatherdata.count()

City          579
Cloudiness    579
Country       579
Date          579
Humidity      579
Lat           579
Lng           579
Max Temp      579
Wind Speed    579
dtype: int64
```

#### This prints the first few rows of the dataframe to view some of the data to ensure it is correct.
```
weatherdata.head()
```
|    | City            |   Cloudiness | Country   |       Date |   Humidity |    Lat |     Lng |   Max Temp |   Wind Speed |
|---:|:----------------|-------------:|:----------|-----------:|-----------:|-------:|--------:|-----------:|-------------:|
|  1 | rikitea         |           72 | PF        | 1556934118 |         74 | -23.12 | -134.97 |      77.25 |        16.8  |
|  2 | yagodnoye       |           26 | RU        | 1556934122 |         99 |  62.52 |  149.63 |      32.59 |         2.51 |
|  3 | atuona          |            0 | PF        | 1556934125 |         79 |  -9.8  | -139.03 |      83.61 |        17.11 |
|  4 | bambous virieux |           75 | MU        | 1556933940 |         88 | -20.34 |   57.76 |      73.4  |         4.7  |
|  5 | xuddur          |           42 | SO        | 1556934130 |         80 |   4.12 |   43.89 |      76.24 |        12.82 |

```
weatherdata.to_csv(output_data_file)
```
### Here we begin plotting all the data

#### Latitude vs. Temperature Plot
```
plt.scatter(x = weatherdata["Lat"], y=weatherdata["Max Temp"], edgecolors='black', alpha=.8)
plt.title("City Latitude vs. Max Temperature (5/03/2019)")
plt.xlabel("Latitude")
plt.ylabel("Max Temperature (F)")
plt.grid()
plt.savefig('output_data/LatitudeVsMaxTemp.png', bbox_inches = 'tight')
plt.show()
```
![Latitude Vs Max Temperature (F)](output_data/LatitudeVsMaxTemp.png?raw=true "Latitude vs Max Temperature (F)")

#### Latitude vs. Humidity Plot
```
plt.scatter(x = weatherdata["Lat"], y=weatherdata["Humidity"], edgecolors='black', alpha=.8)
plt.title("City Latitude vs. Humidity (5/03/2019)")
plt.xlabel("Latitude")
plt.ylabel("Humidity (%)")
plt.grid()
plt.savefig('output_data/LatitudeVsHumidity.png', bbox_inches = 'tight')
plt.show()
```
![Latitude Vs % Humidity](output_data/LatitudeVsHumidity.png?raw=true "Latitude vs % Humidity")  

#### Latitude vs. Cloudiness Plot
```
plt.scatter(x = weatherdata["Lat"], y=weatherdata["Cloudiness"], edgecolors='black', alpha=.8)
plt.title("City Latitude vs. Cloudiness (5/03/2019)")
plt.xlabel("Latitude")
plt.ylabel("Cloudiness (%)")
plt.grid()
plt.savefig('output_data/LatitudeVsCloudiness.png', bbox_inches = 'tight')
plt.show()
```
![Latitude Vs % Cloudiness](output_data/LatitudeVsCloudiness.png?raw=true "Latitude vs % Cloudiness")


#### Latitude vs. Wind Speed Plot
```
plt.scatter(x = weatherdata["Lat"], y=weatherdata["Wind Speed"], edgecolors='black', alpha=.8)
plt.title("City Latitude vs. Wind Speed (5/03/2019)")
plt.xlabel("Latitude")
plt.ylabel("Wind Speed (MPH)")
plt.grid()
plt.savefig('output_data/LatitudeVsWindSpeed.png', bbox_inches = 'tight')
plt.show()
```
![Latitude Vs Wind Speed (MPH)](output_data/LatitudeVsWindSpeed.png?raw=true "Latitude vs Wind Speed (MPH)")

### Morbidly curious, replacing Latitude with Longitude for all of the above scatterplots
#### Longitude versus Temperature Plot
```
plt.scatter(x = weatherdata["Lng"], y=weatherdata["Max Temp"], edgecolors='black', alpha=.8)
plt.title("City Longitude vs. Max Temperature (5/03/2019)")
plt.xlabel("Longitude")
plt.ylabel("Max Temperature (F)")
plt.grid()
plt.savefig('output_data/LongitudeVsMaxTemp.png', bbox_inches = 'tight')
plt.show()
```
![Longitude Vs Max Temperature (F)](output_data/LongitudeVsMaxTemp.png?raw=true "Latitude vs Max Temperature (F)")  



#### Longitude vs. Humidity Plot
```
plt.scatter(x = weatherdata["Lng"], y=weatherdata["Humidity"], edgecolors='black', alpha=.8)
plt.title("City Longitude vs. Humidity (5/03/2019)")
plt.xlabel("Longitude")
plt.ylabel("Humidity (%)")
plt.grid()
plt.savefig('output_data/LongitudeVsHumidity.png', bbox_inches = 'tight')
plt.show()
```
![Longitude Vs % Humidity](output_data/LongitudeVsHumidity.png?raw=true "Latitude vs % Humidity")


#### Longitude vs. Cloudiness Plot
```
plt.scatter(x = weatherdata["Lng"], y=weatherdata["Cloudiness"], edgecolors='black', alpha=.8)
plt.title("City Longitude vs. Cloudiness (5/03/2019)")
plt.xlabel("Longitude")
plt.ylabel("Cloudiness (%)")
plt.grid()
plt.savefig('output_data/LongitudeVsCloudiness.png', bbox_inches = 'tight')
plt.show()
```
![Longitude Vs % Cloudiness](output_data/LongitudeVsCloudiness.png?raw=true "Latitude vs % Cloudiness")


#### Longitude vs. Wind Speed Plot
```
plt.scatter(x = weatherdata["Lng"], y=weatherdata["Wind Speed"], edgecolors='black', alpha=.8)
plt.title("City Longitude vs. Wind Speed (5/03/2019)")
plt.xlabel("Longitude")
plt.ylabel("Wind Speed (MPH)")
plt.grid()
plt.savefig('output_data/LongitudeVsWindSpeed.png', bbox_inches = 'tight')
plt.show()
```
![Longitude Vs Wind Speed (MPH)](output_data/LongitudeVsWindSpeed.png?raw=true "Latitude vs Wind Speed (MPH)")
