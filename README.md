# HW_06_APIs

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
