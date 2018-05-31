# WeatherPy_Homework
Unit 6 Assignment


Analysis
Observed Trend 1: Temperature vs Latitude
               Cities with higher tempertures are closer to the equator than cities with lower tempeatures.

Observed Trend 2: Humidity vs Latitude
               The observed trend is the same as Temperature vs Latitude.  Cities with higher humidity are closer to the equator than cities with lower humidity.

Observed Trend 3: Cloudiness vs Latitude and Wind Speed vs Latitude, The observe trend is that for Cloudiness and Wind        Speed, proximity to the equator does not seem to matter.
#Import Dependancies

from pprint import pprint
from citipy import citipy
from random import uniform

import random
import requests
import json
import openweathermapy.core as owm
from config_wm import api_key

import pandas as pd
import csv
import numpy as np

import matplotlib.pyplot as plt
import matplotlib
%matplotlib inline
import seaborn as sns

import logging
logging.basicConfig(filename="weatherpy.log",level=logging.DEBUG)
# Save config information
url = "http://api.openweathermap.org/data/2.5/weather?"
settings = {"appid":api_key,"units":"imperial"}
#Create Sample Data


lats_lons_df = pd.DataFrame()
cities_df = pd.DataFrame()
cities_list_df = pd.DataFrame()

#lats_lons_df
# x ---> Longitude(-180 +180)
# y ---> Latitude(-90  +90)
for x in range(50):
    row = x
    x, y = uniform(-180,180), uniform(-90, 90)
    gen_lats_lons_df = pd.DataFrame([[x,y]])
    lats_lons_df = lats_lons_df.append(gen_lats_lons_df)

lats_lons_df.index= range(len(lats_lons_df.index))
lats_lons_df.columns = ["Longitude","Latitude"]


#Select Cities - Cities_df
for i, row in lats_lons_df.iterrows():
    latitude = row["Latitude"]
    longitude = row["Longitude"] 
    city = citipy.nearest_city(row["Latitude"],row["Longitude"])
    city_name = city.city_name
    country_code = city.country_code
    gen_cities_df = pd.DataFrame([[city_name,country_code,latitude,longitude]])
    cities_df = cities_df.append(gen_cities_df)

cities_df.index= range(len(cities_df.index))
cities_df.columns = ["City","Country","Latitude","Longitude"]

cities_df

#Perform API Calls
for i, row in cities_df.iterrows():
    sel_city = (row["City"])
    sel_country = (row["Country"])
    settings["q"]=f'{row["City"]},{row["Country"]}'
    try:
        weather_resp = owm.get_current(**settings)
    except:
        sel_city = ""
        sel_country = ""
        pass
    print(weather_resp)
    w_lat = weather_resp.get("coord",{}).get("lat") 
    w_lon = weather_resp.get("coord",{}).get("lon") 
    temp = weather_resp.get("main",{}).get("temp_max")
    w_hum = weather_resp.get("main",{}).get("humidity")
    w_wind = weather_resp.get("wind",{}).get("speed")
    w_clouds = weather_resp.get("clouds",{}).get("all")
    w_city_id = weather_resp.get("id")
    w_city_name = weather_resp.get("name")
    logging.debug(f'Processing... {w_city_id},{w_city_name},{url}{settings["q"]},{settings["units"]}')
    wm_cities_df = pd.DataFrame([[sel_city,sel_country,w_lat,w_lon,temp,w_hum,w_wind,w_clouds]])
    cities_list_df = cities_list_df.append(wm_cities_df)

#Write to CSV File 
cities_list_df.columns = ["City","Country","Latitude","Longitude","Temperature","Humidity","Wind Speed","Cloudiness"]
with open("global_cities_weather_data.csv", "a") as f: 
    cities_list_df.to_csv(f, header=False)
#cities_list_df.to_csv("global_cities_weather_data.csv")
cities_list_df
    
{'coord': {'lon': 147.33, 'lat': -42.88}, 'weather': [{'id': 801, 'main': 'Clouds', 'description': 'few clouds', 'icon': '02n'}], 'base': 'stations', 'main': {'temp': 46.4, 'pressure': 1013, 'humidity': 65, 'temp_min': 46.4, 'temp_max': 46.4}, 'visibility': 10000, 'wind': {'speed': 9.17, 'deg': 330}, 'clouds': {'all': 20}, 'dt': 1527620400, 'sys': {'type': 1, 'id': 8195, 'message': 0.0047, 'country': 'AU', 'sunrise': 1527542972, 'sunset': 1527576388}, 'id': 2163355, 'name': 'Hobart', 'cod': 200}
{'coord': {'lon': 140.83, 'lat': 35.73}, 'weather': [{'id': 803, 'main': 'Clouds', 'description': 'broken clouds', 'icon': '04n'}], 'base': 'stations', 'main': {'temp': 65.08, 'pressure': 1015, 'humidity': 93, 'temp_min': 64.4, 'temp_max': 66.2}, 'visibility': 10000, 'wind': {'speed': 1.12, 'deg': 330}, 'clouds': {'all': 75}, 'dt': 1527622200, 'sys': {'type': 1, 'id': 7505, 'message': 0.0043, 'country': 'JP', 'sunrise': 1527535382, 'sunset': 1527587135}, 'id': 2112802, 'name': 'Hasaki', 'cod': 200}
{'coord': {'lon': 15.64, 'lat': 78.22}, 'weather': [{'id': 803, 'main': 'Clouds', 'description': 'broken clouds', 'icon': '04d'}], 'base': 'stations', 'main': {'temp': 35.6, 'pressure': 1018, 'humidity': 74, 'temp_min': 35.6, 'temp_max': 35.6}, 'visibility': 10000, 'wind': {'speed': 11.41, 'deg': 110}, 'clouds': {'all': 75}, 'dt': 1527619800, 'sys': {'type': 1, 'id': 5326, 'message': 0.0059, 'country': 'SJ', 'sunrise': 0, 'sunset': 0}, 'id': 2729907, 'name': 'Longyearbyen', 'cod': 200}
{'coord': {'lon': 121.18, 'lat': 31.87}, 'weather': [{'id': 804, 'main': 'Clouds', 'description': 'overcast clouds', 'icon': '04n'}], 'base': 'stations', 'main': {'temp': 71.5, 'pressure': 1023.94, 'humidity': 90, 'temp_min': 71.5, 'temp_max': 71.5, 'sea_level': 1024.64, 'grnd_level': 1023.94}, 'wind': {'speed': 6.08, 'deg': 165.501}, 'clouds': {'all': 100}, 'dt': 1527625085, 'sys': {'message': 0.0034, 'country': 'CN', 'sunrise': 1527540666, 'sunset': 1527591281}, 'id': 1801200, 'name': 'Haimen', 'cod': 200}
{'coord': {'lon': 134.26, 'lat': 7.04}, 'weather': [{'id': 520, 'main': 'Rain', 'description': 'light intensity shower rain', 'icon': '09n'}], 'base': 'stations', 'main': {'temp': 76.06, 'pressure': 1009, 'humidity': 94, 'temp_min': 75.2, 'temp_max': 77}, 'visibility': 19312, 'wind': {'speed': 8.77, 'deg': 65.5011}, 'clouds': {'all': 90}, 'dt': 1527619800, 'sys': {'type': 1, 'id': 4055, 'message': 0.0045, 'country': 'PW', 'sunrise': 1527540331, 'sunset': 1527585323}, 'id': 7671223, 'name': 'Kloulklubed', 'cod': 200}
{'coord': {'lon': 134.26, 'lat': 7.04}, 'weather': [{'id': 520, 'main': 'Rain', 'description': 'light intensity shower rain', 'icon': '09n'}], 'base': 'stations', 'main': {'temp': 76.06, 'pressure': 1009, 'humidity': 94, 'temp_min': 75.2, 'temp_max': 77}, 'visibility': 19312, 'wind': {'speed': 8.77, 'deg': 65.5011}, 'clouds': {'all': 90}, 'dt': 1527619800, 'sys': {'type': 1, 'id': 4055, 'message': 0.0045, 'country': 'PW', 'sunrise': 1527540331, 'sunset': 1527585323}, 'id': 7671223, 'name': 'Kloulklubed', 'cod': 200}
{'coord': {'lon': 18.42, 'lat': -33.93}, 'weather': [{'id': 800, 'main': 'Clear', 'description': 'clear sky', 'icon': '01n'}], 'base': 'stations', 'main': {'temp': 48.2, 'pressure': 1021, 'humidity': 93, 'temp_min': 48.2, 'temp_max': 48.2}, 'visibility': 10000, 'wind': {'speed': 5.82, 'deg': 20}, 'clouds': {'all': 0}, 'dt': 1527620400, 'sys': {'type': 1, 'id': 6529, 'message': 0.0045, 'country': 'ZA', 'sunrise': 1527572494, 'sunset': 1527608754}, 'id': 3369157, 'name': 'Cape Town', 'cod': 200}
{'coord': {'lon': 12.69, 'lat': 67.67}, 'weather': [{'id': 521, 'main': 'Rain', 'description': 'shower rain', 'icon': '09d'}], 'base': 'stations', 'main': {'temp': 44.6, 'pressure': 1028, 'humidity': 87, 'temp_min': 44.6, 'temp_max': 44.6}, 'visibility': 10000, 'wind': {'speed': 11.41, 'deg': 290}, 'clouds': {'all': 75}, 'dt': 1527623400, 'sys': {'type': 1, 'id': 5306, 'message': 0.0061, 'country': 'NO', 'sunrise': 0, 'sunset': 0}, 'id': 3137469, 'name': 'Sorland', 'cod': 200}
{'coord': {'lon': -70.91, 'lat': -53.16}, 'weather': [{'id': 802, 'main': 'Clouds', 'description': 'scattered clouds', 'icon': '03d'}], 'base': 'stations', 'main': {'temp': 37.4, 'pressure': 1003, 'humidity': 69, 'temp_min': 37.4, 'temp_max': 37.4}, 'visibility': 10000, 'wind': {'speed': 21.92, 'deg': 250}, 'clouds': {'all': 40}, 'dt': 1527620400, 'sys': {'type': 1, 'id': 4642, 'message': 0.005, 'country': 'CL', 'sunrise': 1527597786, 'sunset': 1527626336}, 'id': 3874787, 'name': 'Punta Arenas', 'cod': 200}
{'coord': {'lon': 54.1, 'lat': 17.01}, 'weather': [{'id': 801, 'main': 'Clouds', 'description': 'few clouds', 'icon': '02n'}], 'base': 'stations', 'main': {'temp': 82.4, 'pressure': 1007, 'humidity': 88, 'temp_min': 82.4, 'temp_max': 82.4}, 'visibility': 10000, 'wind': {'speed': 4.7, 'deg': 190}, 'clouds': {'all': 20}, 'dt': 1527619800, 'sys': {'type': 1, 'id': 7115, 'message': 0.0058, 'country': 'OM', 'sunrise': 1527558557, 'sunset': 1527605582}, 'id': 286621, 'name': 'Salalah', 'cod': 200}
{'coord': {'lon': 93.51, 'lat': 42.84}, 'weather': [{'id': 801, 'main': 'Clouds', 'description': 'few clouds', 'icon': '02n'}], 'base': 'stations', 'main': {'temp': 55.48, 'pressure': 940.93, 'humidity': 37, 'temp_min': 55.48, 'temp_max': 55.48, 'sea_level': 1021.07, 'grnd_level': 940.93}, 'wind': {'speed': 4.52, 'deg': 115.001}, 'clouds': {'all': 12}, 'dt': 1527625087, 'sys': {'message': 0.0037, 'country': 'CN', 'sunrise': 1527545492, 'sunset': 1527599748}, 'id': 1529484, 'name': 'Hami', 'cod': 200}
{'coord': {'lon': 110.4, 'lat': 18.8}, 'weather': [{'id': 800, 'main': 'Clear', 'description': 'clear sky', 'icon': '02n'}], 'base': 'stations', 'main': {'temp': 84.01, 'pressure': 1019.24, 'humidity': 100, 'temp_min': 84.01, 'temp_max': 84.01, 'sea_level': 1022.73, 'grnd_level': 1019.24}, 'wind': {'speed': 6.2, 'deg': 184.001}, 'clouds': {'all': 8}, 'dt': 1527625087, 'sys': {'message': 0.0031, 'country': 'CN', 'sunrise': 1527544853, 'sunset': 1527592260}, 'id': 1791779, 'name': 'Wanning', 'cod': 200}
{'coord': {'lon': -70.91, 'lat': -53.16}, 'weather': [{'id': 802, 'main': 'Clouds', 'description': 'scattered clouds', 'icon': '03d'}], 'base': 'stations', 'main': {'temp': 37.4, 'pressure': 1003, 'humidity': 69, 'temp_min': 37.4, 'temp_max': 37.4}, 'visibility': 10000, 'wind': {'speed': 21.92, 'deg': 250}, 'clouds': {'all': 40}, 'dt': 1527620400, 'sys': {'type': 1, 'id': 4642, 'message': 0.005, 'country': 'CL', 'sunrise': 1527597786, 'sunset': 1527626336}, 'id': 3874787, 'name': 'Punta Arenas', 'cod': 200}
{'coord': {'lon': -70.91, 'lat': -53.16}, 'weather': [{'id': 802, 'main': 'Clouds', 'description': 'scattered clouds', 'icon': '03d'}], 'base': 'stations', 'main': {'temp': 37.4, 'pressure': 1003, 'humidity': 69, 'temp_min': 37.4, 'temp_max': 37.4}, 'visibility': 10000, 'wind': {'speed': 21.92, 'deg': 250}, 'clouds': {'all': 40}, 'dt': 1527620400, 'sys': {'type': 1, 'id': 4642, 'message': 0.005, 'country': 'CL', 'sunrise': 1527597786, 'sunset': 1527626336}, 'id': 3874787, 'name': 'Punta Arenas', 'cod': 200}
{'coord': {'lon': -90.35, 'lat': -0.74}, 'weather': [{'id': 801, 'main': 'Clouds', 'description': 'few clouds', 'icon': '02d'}], 'base': 'stations', 'main': {'temp': 80.6, 'pressure': 1013, 'humidity': 65, 'temp_min': 80.6, 'temp_max': 80.6}, 'visibility': 10000, 'wind': {'speed': 17.22, 'deg': 110}, 'clouds': {'all': 20}, 'dt': 1527620400, 'sys': {'type': 1, 'id': 4355, 'message': 0.0031, 'country': 'EC', 'sunrise': 1527594991, 'sunset': 1527638484}, 'id': 3652764, 'name': 'Puerto Ayora', 'cod': 200}
{'coord': {'lon': 139.9, 'lat': 69.3}, 'weather': [{'id': 802, 'main': 'Clouds', 'description': 'scattered clouds', 'icon': '03d'}], 'base': 'stations', 'main': {'temp': 33.34, 'pressure': 954.23, 'humidity': 95, 'temp_min': 33.34, 'temp_max': 33.34, 'sea_level': 1019.73, 'grnd_level': 954.23}, 'wind': {'speed': 3.96, 'deg': 317.001}, 'clouds': {'all': 32}, 'dt': 1527625088, 'sys': {'message': 0.0674, 'country': 'RU', 'sunrise': 0, 'sunset': 0}, 'id': 2028164, 'name': 'Deputatskiy', 'cod': 200}
{'coord': {'lon': 33.64, 'lat': -25.04}, 'weather': [{'id': 800, 'main': 'Clear', 'description': 'clear sky', 'icon': '01n'}], 'base': 'stations', 'main': {'temp': 72.49, 'pressure': 1027.59, 'humidity': 95, 'temp_min': 72.49, 'temp_max': 72.49, 'sea_level': 1030.11, 'grnd_level': 1027.59}, 'wind': {'speed': 10.22, 'deg': 324.501}, 'clouds': {'all': 0}, 'dt': 1527624801, 'sys': {'message': 0.0038, 'country': 'MZ', 'sunrise': 1527567709, 'sunset': 1527606236}, 'id': 1024552, 'name': 'Xai-Xai', 'cod': 200}
{'coord': {'lon': -161.76, 'lat': 60.79}, 'weather': [{'id': 500, 'main': 'Rain', 'description': 'light rain', 'icon': '10d'}], 'base': 'stations', 'main': {'temp': 44.6, 'pressure': 1007, 'humidity': 75, 'temp_min': 44.6, 'temp_max': 44.6}, 'visibility': 16093, 'wind': {'speed': 14.99, 'deg': 150}, 'clouds': {'all': 90}, 'dt': 1527619980, 'sys': {'type': 1, 'id': 28, 'message': 0.0039, 'country': 'US', 'sunrise': 1527600676, 'sunset': 1527667163}, 'id': 5880568, 'name': 'Bethel', 'cod': 200}
{'coord': {'lon': 153.71, 'lat': 67.46}, 'weather': [{'id': 800, 'main': 'Clear', 'description': 'clear sky', 'icon': '01n'}], 'base': 'stations', 'main': {'temp': 47.92, 'pressure': 999.94, 'humidity': 67, 'temp_min': 47.92, 'temp_max': 47.92, 'sea_level': 1009.85, 'grnd_level': 999.94}, 'wind': {'speed': 3.18, 'deg': 26.5011}, 'clouds': {'all': 0}, 'dt': 1527625089, 'sys': {'message': 0.0036, 'country': 'RU', 'sunrise': 0, 'sunset': 0}, 'id': 2121025, 'name': 'Srednekolymsk', 'cod': 200}
{'coord': {'lon': -73.76, 'lat': -42.48}, 'weather': [{'id': 500, 'main': 'Rain', 'description': 'light rain', 'icon': '10d'}], 'base': 'stations', 'main': {'temp': 46.75, 'pressure': 1016.81, 'humidity': 97, 'temp_min': 46.75, 'temp_max': 46.75, 'sea_level': 1031.73, 'grnd_level': 1016.81}, 'wind': {'speed': 6.2, 'deg': 273.501}, 'rain': {'3h': 1}, 'clouds': {'all': 48}, 'dt': 1527625089, 'sys': {'message': 0.004, 'country': 'CL', 'sunrise': 1527595992, 'sunset': 1527629503}, 'id': 3896218, 'name': 'Castro', 'cod': 200}
{'coord': {'lon': 102.47, 'lat': 71.98}, 'weather': [{'id': 800, 'main': 'Clear', 'description': 'clear sky', 'icon': '01d'}], 'base': 'stations', 'main': {'temp': 33.25, 'pressure': 1015.43, 'humidity': 86, 'temp_min': 33.25, 'temp_max': 33.25, 'sea_level': 1020.18, 'grnd_level': 1015.43}, 'wind': {'speed': 9.78, 'deg': 84.5011}, 'clouds': {'all': 0}, 'dt': 1527624800, 'sys': {'message': 0.0042, 'country': 'RU', 'sunrise': 0, 'sunset': 0}, 'id': 2022572, 'name': 'Khatanga', 'cod': 200}
{'coord': {'lon': 76.04, 'lat': 20.02}, 'weather': [{'id': 800, 'main': 'Clear', 'description': 'clear sky', 'icon': '01n'}], 'base': 'stations', 'main': {'temp': 81.67, 'pressure': 957.14, 'humidity': 47, 'temp_min': 81.67, 'temp_max': 81.67, 'sea_level': 1013.33, 'grnd_level': 957.14}, 'wind': {'speed': 12.24, 'deg': 300.501}, 'clouds': {'all': 0}, 'dt': 1527625089, 'sys': {'message': 0.0041, 'country': 'IN', 'sunrise': 1527552964, 'sunset': 1527600644}, 'id': 1273136, 'name': 'Deulgaon Raja', 'cod': 200}
{'coord': {'lon': 28.68, 'lat': -15.33}, 'weather': [{'id': 800, 'main': 'Clear', 'description': 'clear sky', 'icon': '01n'}], 'base': 'stations', 'main': {'temp': 57.37, 'pressure': 916.05, 'humidity': 74, 'temp_min': 57.37, 'temp_max': 57.37, 'sea_level': 1030.6, 'grnd_level': 916.05}, 'wind': {'speed': 4.74, 'deg': 81.5011}, 'clouds': {'all': 0}, 'dt': 1527625089, 'sys': {'message': 0.0039, 'country': 'ZM', 'sunrise': 1527567847, 'sunset': 1527608483}, 'id': 917688, 'name': 'Chongwe', 'cod': 200}
{'coord': {'lon': 28.68, 'lat': -15.33}, 'weather': [{'id': 800, 'main': 'Clear', 'description': 'clear sky', 'icon': '01n'}], 'base': 'stations', 'main': {'temp': 57.37, 'pressure': 916.05, 'humidity': 74, 'temp_min': 57.37, 'temp_max': 57.37, 'sea_level': 1030.6, 'grnd_level': 916.05}, 'wind': {'speed': 4.74, 'deg': 81.5011}, 'clouds': {'all': 0}, 'dt': 1527625089, 'sys': {'message': 0.0039, 'country': 'ZM', 'sunrise': 1527567847, 'sunset': 1527608483}, 'id': 917688, 'name': 'Chongwe', 'cod': 200}
{'coord': {'lon': 169.85, 'lat': -46.28}, 'weather': [{'id': 800, 'main': 'Clear', 'description': 'clear sky', 'icon': '01n'}], 'base': 'stations', 'main': {'temp': 24.07, 'pressure': 1019.97, 'humidity': 67, 'temp_min': 24.07, 'temp_max': 24.07, 'sea_level': 1037.73, 'grnd_level': 1019.97}, 'wind': {'speed': 3.74, 'deg': 345.501}, 'clouds': {'all': 0}, 'dt': 1527625090, 'sys': {'message': 0.0035, 'country': 'NZ', 'sunrise': 1527538237, 'sunset': 1527570312}, 'id': 2208248, 'name': 'Kaitangata', 'cod': 200}
{'coord': {'lon': 169.85, 'lat': -46.28}, 'weather': [{'id': 800, 'main': 'Clear', 'description': 'clear sky', 'icon': '01n'}], 'base': 'stations', 'main': {'temp': 24.07, 'pressure': 1019.97, 'humidity': 67, 'temp_min': 24.07, 'temp_max': 24.07, 'sea_level': 1037.73, 'grnd_level': 1019.97}, 'wind': {'speed': 3.74, 'deg': 345.501}, 'clouds': {'all': 0}, 'dt': 1527625090, 'sys': {'message': 0.0035, 'country': 'NZ', 'sunrise': 1527538237, 'sunset': 1527570312}, 'id': 2208248, 'name': 'Kaitangata', 'cod': 200}
{'coord': {'lon': -65.41, 'lat': -24.79}, 'weather': [{'id': 800, 'main': 'Clear', 'description': 'clear sky', 'icon': '01d'}], 'base': 'stations', 'main': {'temp': 77, 'pressure': 1010, 'humidity': 47, 'temp_min': 77, 'temp_max': 77}, 'visibility': 10000, 'wind': {'speed': 9.17, 'deg': 70}, 'clouds': {'all': 0}, 'dt': 1527620400, 'sys': {'type': 1, 'id': 4729, 'message': 0.0035, 'country': 'AR', 'sunrise': 1527591460, 'sunset': 1527630034}, 'id': 3838233, 'name': 'Salta', 'cod': 200}
{'coord': {'lon': -70.91, 'lat': -53.16}, 'weather': [{'id': 802, 'main': 'Clouds', 'description': 'scattered clouds', 'icon': '03d'}], 'base': 'stations', 'main': {'temp': 37.4, 'pressure': 1003, 'humidity': 69, 'temp_min': 37.4, 'temp_max': 37.4}, 'visibility': 10000, 'wind': {'speed': 21.92, 'deg': 250}, 'clouds': {'all': 40}, 'dt': 1527620400, 'sys': {'type': 1, 'id': 4642, 'message': 0.005, 'country': 'CL', 'sunrise': 1527597786, 'sunset': 1527626336}, 'id': 3874787, 'name': 'Punta Arenas', 'cod': 200}
{'coord': {'lon': -60.62, 'lat': -3.3}, 'weather': [{'id': 802, 'main': 'Clouds', 'description': 'scattered clouds', 'icon': '03d'}], 'base': 'stations', 'main': {'temp': 84.91, 'pressure': 1019.72, 'humidity': 71, 'temp_min': 84.91, 'temp_max': 84.91, 'sea_level': 1022.94, 'grnd_level': 1019.72}, 'wind': {'speed': 5.19, 'deg': 76.0011}, 'clouds': {'all': 36}, 'dt': 1527625090, 'sys': {'message': 0.0037, 'country': 'BR', 'sunrise': 1527588100, 'sunset': 1527631101}, 'id': 3663529, 'name': 'Manacapuru', 'cod': 200}
{'coord': {'lon': -68.31, 'lat': -54.81}, 'weather': [{'id': 621, 'main': 'Snow', 'description': 'shower snow', 'icon': '13d'}], 'base': 'stations', 'main': {'temp': 34.05, 'pressure': 1000, 'humidity': 100, 'temp_min': 32, 'temp_max': 35.6}, 'visibility': 4000, 'wind': {'speed': 23.04, 'deg': 250, 'gust': 17.5}, 'clouds': {'all': 40}, 'dt': 1527620400, 'sys': {'type': 1, 'id': 4754, 'message': 0.0043, 'country': 'AR', 'sunrise': 1527597682, 'sunset': 1527625190}, 'id': 3833367, 'name': 'Ushuaia', 'cod': 200}
{'coord': {'lon': 134.5, 'lat': 33.93}, 'weather': [{'id': 500, 'main': 'Rain', 'description': 'light rain', 'icon': '10n'}, {'id': 701, 'main': 'Mist', 'description': 'mist', 'icon': '50n'}], 'base': 'stations', 'main': {'temp': 68, 'pressure': 1012, 'humidity': 94, 'temp_min': 68, 'temp_max': 68}, 'visibility': 4900, 'wind': {'speed': 3.36, 'deg': 210}, 'clouds': {'all': 90}, 'dt': 1527623220, 'sys': {'type': 1, 'id': 7586, 'message': 0.0044, 'country': 'JP', 'sunrise': 1527537173, 'sunset': 1527588380}, 'id': 1865309, 'name': 'Katsuura', 'cod': 200}
{'coord': {'lon': 19.24, 'lat': -34.42}, 'weather': [{'id': 801, 'main': 'Clouds', 'description': 'few clouds', 'icon': '02n'}], 'base': 'stations', 'main': {'temp': 39.82, 'pressure': 994.43, 'humidity': 97, 'temp_min': 39.82, 'temp_max': 39.82, 'sea_level': 1035.1, 'grnd_level': 994.43}, 'wind': {'speed': 2.95, 'deg': 340.001}, 'clouds': {'all': 24}, 'dt': 1527625091, 'sys': {'message': 0.004, 'country': 'ZA', 'sunrise': 1527572367, 'sunset': 1527608488}, 'id': 3366880, 'name': 'Hermanus', 'cod': 200}
{'coord': {'lon': -14.42, 'lat': -7.93}, 'weather': [{'id': 800, 'main': 'Clear', 'description': 'clear sky', 'icon': '01n'}], 'base': 'stations', 'main': {'temp': 79.15, 'pressure': 1028.07, 'humidity': 100, 'temp_min': 79.15, 'temp_max': 79.15, 'sea_level': 1028.17, 'grnd_level': 1028.07}, 'wind': {'speed': 15.48, 'deg': 122.501}, 'clouds': {'all': 0}, 'dt': 1527625091, 'sys': {'message': 0.0046, 'country': 'SH', 'sunrise': 1527577457, 'sunset': 1527619565}, 'id': 2411397, 'name': 'Georgetown', 'cod': 200}
{'coord': {'lon': -68.31, 'lat': -54.81}, 'weather': [{'id': 621, 'main': 'Snow', 'description': 'shower snow', 'icon': '13d'}], 'base': 'stations', 'main': {'temp': 34.05, 'pressure': 1000, 'humidity': 100, 'temp_min': 32, 'temp_max': 35.6}, 'visibility': 4000, 'wind': {'speed': 23.04, 'deg': 250, 'gust': 17.5}, 'clouds': {'all': 40}, 'dt': 1527620400, 'sys': {'type': 1, 'id': 4754, 'message': 0.0043, 'country': 'AR', 'sunrise': 1527597682, 'sunset': 1527625190}, 'id': 3833367, 'name': 'Ushuaia', 'cod': 200}
{'coord': {'lon': -73.14, 'lat': -40.57}, 'weather': [{'id': 803, 'main': 'Clouds', 'description': 'broken clouds', 'icon': '04d'}], 'base': 'stations', 'main': {'temp': 51.8, 'pressure': 1020, 'humidity': 71, 'temp_min': 51.8, 'temp_max': 51.8}, 'visibility': 10000, 'wind': {'speed': 5.82, 'deg': 200}, 'clouds': {'all': 75}, 'dt': 1527620400, 'sys': {'type': 1, 'id': 4657, 'message': 0.007, 'country': 'CL', 'sunrise': 1527595505, 'sunset': 1527629693}, 'id': 3877949, 'name': 'Osorno', 'cod': 200}
{'coord': {'lon': -5.72, 'lat': -15.94}, 'weather': [{'id': 802, 'main': 'Clouds', 'description': 'scattered clouds', 'icon': '03n'}], 'base': 'stations', 'main': {'temp': 73.03, 'pressure': 1033.18, 'humidity': 100, 'temp_min': 73.03, 'temp_max': 73.03, 'sea_level': 1033.23, 'grnd_level': 1033.18}, 'wind': {'speed': 21.63, 'deg': 113.001}, 'clouds': {'all': 44}, 'dt': 1527623119, 'sys': {'message': 0.0063, 'country': 'SH', 'sunrise': 1527576167, 'sunset': 1527616676}, 'id': 3370903, 'name': 'Jamestown', 'cod': 200}
{'coord': {'lon': 117.88, 'lat': -35.02}, 'weather': [{'id': 800, 'main': 'Clear', 'description': 'clear sky', 'icon': '01n'}], 'base': 'stations', 'main': {'temp': 51.25, 'pressure': 1023.29, 'humidity': 90, 'temp_min': 51.25, 'temp_max': 51.25, 'sea_level': 1036.39, 'grnd_level': 1023.29}, 'wind': {'speed': 7.87, 'deg': 345.501}, 'clouds': {'all': 0}, 'dt': 1527624315, 'sys': {'message': 0.0034, 'country': 'AU', 'sunrise': 1527548768, 'sunset': 1527584734}, 'id': 2077963, 'name': 'Albany', 'cod': 200}
{'coord': {'lon': 117.88, 'lat': -35.02}, 'weather': [{'id': 800, 'main': 'Clear', 'description': 'clear sky', 'icon': '01n'}], 'base': 'stations', 'main': {'temp': 51.25, 'pressure': 1023.29, 'humidity': 90, 'temp_min': 51.25, 'temp_max': 51.25, 'sea_level': 1036.39, 'grnd_level': 1023.29}, 'wind': {'speed': 7.87, 'deg': 345.501}, 'clouds': {'all': 0}, 'dt': 1527624315, 'sys': {'message': 0.0034, 'country': 'AU', 'sunrise': 1527548768, 'sunset': 1527584734}, 'id': 2077963, 'name': 'Albany', 'cod': 200}
{'coord': {'lon': 39.51, 'lat': 68.05}, 'weather': [{'id': 500, 'main': 'Rain', 'description': 'light rain', 'icon': '10d'}], 'base': 'stations', 'main': {'temp': 33.97, 'pressure': 994.92, 'humidity': 100, 'temp_min': 33.97, 'temp_max': 33.97, 'sea_level': 1020.79, 'grnd_level': 994.92}, 'wind': {'speed': 16.26, 'deg': 15.0011}, 'rain': {'3h': 1.745}, 'clouds': {'all': 92}, 'dt': 1527625092, 'sys': {'message': 0.0038, 'country': 'RU', 'sunrise': 0, 'sunset': 0}, 'id': 556268, 'name': 'Ostrovnoy', 'cod': 200}
{'coord': {'lon': 39.51, 'lat': 68.05}, 'weather': [{'id': 500, 'main': 'Rain', 'description': 'light rain', 'icon': '10d'}], 'base': 'stations', 'main': {'temp': 33.97, 'pressure': 994.92, 'humidity': 100, 'temp_min': 33.97, 'temp_max': 33.97, 'sea_level': 1020.79, 'grnd_level': 994.92}, 'wind': {'speed': 16.26, 'deg': 15.0011}, 'rain': {'3h': 1.745}, 'clouds': {'all': 92}, 'dt': 1527625092, 'sys': {'message': 0.0038, 'country': 'RU', 'sunrise': 0, 'sunset': 0}, 'id': 556268, 'name': 'Ostrovnoy', 'cod': 200}
{'coord': {'lon': -42.27, 'lat': -2.76}, 'weather': [{'id': 801, 'main': 'Clouds', 'description': 'few clouds', 'icon': '02d'}], 'base': 'stations', 'main': {'temp': 89.6, 'pressure': 1010, 'humidity': 62, 'temp_min': 89.6, 'temp_max': 89.6}, 'visibility': 10000, 'wind': {'speed': 5.82, 'deg': 90}, 'clouds': {'all': 20}, 'dt': 1527620400, 'sys': {'type': 1, 'id': 4549, 'message': 0.0041, 'country': 'BR', 'sunrise': 1527583644, 'sunset': 1527626749}, 'id': 3385899, 'name': 'Tutoia', 'cod': 200}
{'coord': {'lon': -161.76, 'lat': 60.79}, 'weather': [{'id': 500, 'main': 'Rain', 'description': 'light rain', 'icon': '10d'}], 'base': 'stations', 'main': {'temp': 44.6, 'pressure': 1007, 'humidity': 75, 'temp_min': 44.6, 'temp_max': 44.6}, 'visibility': 16093, 'wind': {'speed': 14.99, 'deg': 150}, 'clouds': {'all': 90}, 'dt': 1527619980, 'sys': {'type': 1, 'id': 28, 'message': 0.0039, 'country': 'US', 'sunrise': 1527600676, 'sunset': 1527667163}, 'id': 5880568, 'name': 'Bethel', 'cod': 200}
{'coord': {'lon': 112.4, 'lat': 66.42}, 'weather': [{'id': 803, 'main': 'Clouds', 'description': 'broken clouds', 'icon': '04n'}], 'base': 'stations', 'main': {'temp': 36.4, 'pressure': 975.14, 'humidity': 73, 'temp_min': 36.4, 'temp_max': 36.4, 'sea_level': 1026.34, 'grnd_level': 975.14}, 'wind': {'speed': 4.41, 'deg': 144.001}, 'clouds': {'all': 56}, 'dt': 1527625093, 'sys': {'message': 0.006, 'country': 'RU', 'sunrise': 1527529273, 'sunset': 1527607157}, 'id': 2014624, 'name': 'Udachnyy', 'cod': 200}
{'coord': {'lon': 112.4, 'lat': 66.42}, 'weather': [{'id': 803, 'main': 'Clouds', 'description': 'broken clouds', 'icon': '04n'}], 'base': 'stations', 'main': {'temp': 36.4, 'pressure': 975.14, 'humidity': 73, 'temp_min': 36.4, 'temp_max': 36.4, 'sea_level': 1026.34, 'grnd_level': 975.14}, 'wind': {'speed': 4.41, 'deg': 144.001}, 'clouds': {'all': 56}, 'dt': 1527625093, 'sys': {'message': 0.006, 'country': 'RU', 'sunrise': 1527529273, 'sunset': 1527607157}, 'id': 2014624, 'name': 'Udachnyy', 'cod': 200}
{'coord': {'lon': 123.37, 'lat': 66.77}, 'weather': [{'id': 801, 'main': 'Clouds', 'description': 'few clouds', 'icon': '02n'}], 'base': 'stations', 'main': {'temp': 39.64, 'pressure': 1019, 'humidity': 63, 'temp_min': 39.64, 'temp_max': 39.64, 'sea_level': 1027.15, 'grnd_level': 1019}, 'wind': {'speed': 4.97, 'deg': 337.501}, 'clouds': {'all': 24}, 'dt': 1527625094, 'sys': {'message': 0.0039, 'country': 'RU', 'sunrise': 1527525895, 'sunset': 1527605344}, 'id': 2012530, 'name': 'Zhigansk', 'cod': 200}
{'coord': {'lon': -109.91, 'lat': 22.89}, 'weather': [{'id': 800, 'main': 'Clear', 'description': 'clear sky', 'icon': '02d'}], 'base': 'stations', 'main': {'temp': 90.81, 'pressure': 1012, 'humidity': 37, 'temp_min': 86, 'temp_max': 95}, 'visibility': 14484, 'wind': {'speed': 11.41, 'deg': 140}, 'clouds': {'all': 5}, 'dt': 1527622800, 'sys': {'type': 1, 'id': 4016, 'message': 0.0034, 'country': 'MX', 'sunrise': 1527597261, 'sunset': 1527645613}, 'id': 3985710, 'name': 'Cabo San Lucas', 'cod': 200}
{'coord': {'lon': 140.83, 'lat': 35.73}, 'weather': [{'id': 803, 'main': 'Clouds', 'description': 'broken clouds', 'icon': '04n'}], 'base': 'stations', 'main': {'temp': 65.08, 'pressure': 1015, 'humidity': 93, 'temp_min': 64.4, 'temp_max': 66.2}, 'visibility': 10000, 'wind': {'speed': 1.12, 'deg': 330}, 'clouds': {'all': 75}, 'dt': 1527622200, 'sys': {'type': 1, 'id': 7505, 'message': 0.0043, 'country': 'JP', 'sunrise': 1527535382, 'sunset': 1527587135}, 'id': 2112802, 'name': 'Hasaki', 'cod': 200}
{'coord': {'lon': 51.57, 'lat': 31.41}, 'weather': [{'id': 800, 'main': 'Clear', 'description': 'clear sky', 'icon': '01n'}], 'base': 'stations', 'main': {'temp': 46.84, 'pressure': 768.68, 'humidity': 88, 'temp_min': 46.84, 'temp_max': 46.84, 'sea_level': 1020.95, 'grnd_level': 768.68}, 'wind': {'speed': 2.06, 'deg': 127.001}, 'clouds': {'all': 0}, 'dt': 1527624901, 'sys': {'message': 0.0042, 'country': 'IR', 'sunrise': 1527557433, 'sunset': 1527607929}, 'id': 116406, 'name': 'Semirom', 'cod': 200}
{'coord': {'lon': 173.16, 'lat': -35.17}, 'weather': [{'id': 800, 'main': 'Clear', 'description': 'clear sky', 'icon': '01n'}], 'base': 'stations', 'main': {'temp': 46.21, 'pressure': 1022.16, 'humidity': 100, 'temp_min': 46.21, 'temp_max': 46.21, 'sea_level': 1035.78, 'grnd_level': 1022.16}, 'wind': {'speed': 2.84, 'deg': 101.001}, 'clouds': {'all': 0}, 'dt': 1527625094, 'sys': {'message': 0.0039, 'country': 'NZ', 'sunrise': 1527535517, 'sunset': 1527571448}, 'id': 2194098, 'name': 'Ahipara', 'cod': 200}
{'coord': {'lon': -134.97, 'lat': -23.12}, 'weather': [{'id': 800, 'main': 'Clear', 'description': 'clear sky', 'icon': '02d'}], 'base': 'stations', 'main': {'temp': 75.91, 'pressure': 1028.88, 'humidity': 100, 'temp_min': 75.91, 'temp_max': 75.91, 'sea_level': 1028.89, 'grnd_level': 1028.88}, 'wind': {'speed': 10, 'deg': 159.501}, 'clouds': {'all': 8}, 'dt': 1527624146, 'sys': {'message': 0.0062, 'country': 'PF', 'sunrise': 1527607968, 'sunset': 1527646919}, 'id': 4030556, 'name': 'Rikitea', 'cod': 200}
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }
.dataframe tbody tr th {
    vertical-align: top;
}

.dataframe thead th {
    text-align: right;
}
</style>
City	Country	Latitude	Longitude	Temperature	Humidity	Wind Speed	Cloudiness
0			-42.88	147.33	46.40	65	9.17	20
0	hasaki	jp	35.73	140.83	66.20	93	1.12	75
0	longyearbyen	sj	78.22	15.64	35.60	74	11.41	75
0	haimen	cn	31.87	121.18	71.50	90	6.08	100
0	kloulklubed	pw	7.04	134.26	77.00	94	8.77	90
0			7.04	134.26	77.00	94	8.77	90
0	cape town	za	-33.93	18.42	48.20	93	5.82	0
0	sorland	no	67.67	12.69	44.60	87	11.41	75
0	punta arenas	cl	-53.16	-70.91	37.40	69	21.92	40
0	salalah	om	17.01	54.10	82.40	88	4.70	20
0	hami	cn	42.84	93.51	55.48	37	4.52	12
0	wanning	cn	18.80	110.40	84.01	100	6.20	8
0	punta arenas	cl	-53.16	-70.91	37.40	69	21.92	40
0	punta arenas	cl	-53.16	-70.91	37.40	69	21.92	40
0	puerto ayora	ec	-0.74	-90.35	80.60	65	17.22	20
0	deputatskiy	ru	69.30	139.90	33.34	95	3.96	32
0	xai-xai	mz	-25.04	33.64	72.49	95	10.22	0
0	bethel	us	60.79	-161.76	44.60	75	14.99	90
0	srednekolymsk	ru	67.46	153.71	47.92	67	3.18	0
0	castro	cl	-42.48	-73.76	46.75	97	6.20	48
0	khatanga	ru	71.98	102.47	33.25	86	9.78	0
0	deulgaon raja	in	20.02	76.04	81.67	47	12.24	0
0	chongwe	zm	-15.33	28.68	57.37	74	4.74	0
0			-15.33	28.68	57.37	74	4.74	0
0	kaitangata	nz	-46.28	169.85	24.07	67	3.74	0
0			-46.28	169.85	24.07	67	3.74	0
0	salta	ar	-24.79	-65.41	77.00	47	9.17	0
0	punta arenas	cl	-53.16	-70.91	37.40	69	21.92	40
0	manacapuru	br	-3.30	-60.62	84.91	71	5.19	36
0	ushuaia	ar	-54.81	-68.31	35.60	100	23.04	40
0	katsuura	jp	33.93	134.50	68.00	94	3.36	90
0	hermanus	za	-34.42	19.24	39.82	97	2.95	24
0	georgetown	sh	-7.93	-14.42	79.15	100	15.48	0
0	ushuaia	ar	-54.81	-68.31	35.60	100	23.04	40
0	osorno	cl	-40.57	-73.14	51.80	71	5.82	75
0	jamestown	sh	-15.94	-5.72	73.03	100	21.63	44
0	albany	au	-35.02	117.88	51.25	90	7.87	0
0			-35.02	117.88	51.25	90	7.87	0
0	ostrovnoy	ru	68.05	39.51	33.97	100	16.26	92
0			68.05	39.51	33.97	100	16.26	92
0	tutoia	br	-2.76	-42.27	89.60	62	5.82	20
0	bethel	us	60.79	-161.76	44.60	75	14.99	90
0	udachnyy	ru	66.42	112.40	36.40	73	4.41	56
0			66.42	112.40	36.40	73	4.41	56
0	zhigansk	ru	66.77	123.37	39.64	63	4.97	24
0	cabo san lucas	mx	22.89	-109.91	95.00	37	11.41	5
0	hasaki	jp	35.73	140.83	66.20	93	1.12	75
0	semirom	ir	31.41	51.57	46.84	88	2.06	0
0	ahipara	nz	-35.17	173.16	46.21	100	2.84	0
0	rikitea	pf	-23.12	-134.97	75.91	100	10.00	8
# Create a path to the Global City Data csv and read it into a Pandas DataFrame
csv_path = "global_cities_weather_data.csv"
cities_list_df = pd.read_csv(csv_path)


with open(csv_path, newline='') as csvfile:

    # CSV reader specifies delimiter and variable that holds contents
    csvreader = csv.reader(csv_path, delimiter=',')
    # Skip the Headers
    next(csvreader,None)
    
#Remove Blank Cities (No City Data) and Duplicates
cities_list_df.columns = [["I","City","Country","Latitude","Longitude","Temperature","Humidity","Wind Speed","Cloudiness"]]
cities_list_df.reset_index()
cities_list_df.drop(["I"],1, inplace=True,level=0)
cities_list_df.set_index("City")
no_dups_cities_list_df = cities_list_df.dropna(axis='rows')
no_dups_cities_list_df2 = no_dups_cities_list_df.drop_duplicates()
no_dups_cities_list_df2
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }
.dataframe tbody tr th {
    vertical-align: top;
}

.dataframe thead tr th {
    text-align: left;
}
</style>
City	Country	Latitude	Longitude	Temperature	Humidity	Wind Speed	Cloudiness
0	yarmouth	ca	43.84	-66.12	64.40	72	9.17	20
1	punta arenas	cl	-53.16	-70.91	37.40	64	24.16	20
2	rikitea	pf	-23.12	-134.97	76.28	100	10.76	80
3	dikson	ru	73.51	80.55	29.66	97	3.38	88
5	kavieng	pg	-2.57	150.80	83.57	100	2.59	76
8	albany	au	-35.02	117.88	53.78	86	7.63	68
11	hermiston	us	45.84	-119.29	73.40	28	11.41	1
12	wanning	cn	18.80	110.40	84.47	100	7.52	32
13	port blair	in	11.67	92.75	80.96	100	13.33	80
14	san andres	co	8.22	-76.58	83.03	100	3.71	24
15	cape town	za	-33.93	18.42	50.00	93	6.93	0
17	khatanga	ru	71.98	102.47	29.03	81	3.27	0
18	saint-augustin	ca	45.63	-73.98	75.20	36	9.17	20
20	kapaa	us	22.08	-159.32	77.00	88	13.87	75
22	fortuna	us	40.60	-124.16	53.60	81	11.41	75
23	ushuaia	ar	-54.81	-68.31	37.40	80	24.16	40
24	yumen	cn	40.29	97.04	50.36	61	3.15	0
25	bethel	us	60.79	-161.76	44.60	75	14.99	90
26	georgetown	sh	-7.93	-14.42	79.34	100	14.23	8
27	esperance	au	-33.86	121.89	45.41	68	9.75	0
28	longyearbyen	sj	78.22	15.64	35.60	74	11.41	75
30	barrow	us	39.51	-90.40	80.60	69	3.36	40
31	east london	za	-33.02	27.91	55.40	71	9.17	0
32	palaikastron	gr	35.20	26.25	71.51	95	21.16	0
34	port elizabeth	za	-33.92	25.57	57.20	76	14.99	0
35	yellowknife	ca	62.45	-114.38	50.00	34	19.46	20
36	ribeira grande	pt	38.52	-28.70	68.00	64	16.11	40
37	cherskiy	ru	68.75	161.30	41.99	73	4.72	92
39	guerrero negro	mx	27.97	-114.04	65.21	78	9.86	24
40	mar del plata	ar	-46.43	-67.52	49.73	60	13.89	0
...	...	...	...	...	...	...	...	...
1078	luanda	ao	-8.83	13.24	73.03	98	4.97	0
1080	yellowknife	ca	62.45	-114.38	51.80	37	20.80	20
1082	new norfolk	au	-42.78	147.06	44.60	70	8.05	20
1086	matara	lk	5.95	80.54	80.14	96	18.95	76
1094	doctor cecilio baez	py	-25.05	-56.32	78.07	45	6.64	0
1095	rawson	ar	-43.30	-65.11	49.99	41	6.42	12
1096	jacutinga	br	-22.29	-46.61	68.00	45	6.93	20
1098	bahia honda	cu	22.90	-83.16	73.21	100	4.63	92
1101	hasaki	jp	35.73	140.83	66.20	93	1.12	75
1103	haimen	cn	31.87	121.18	71.50	90	6.08	100
1104	kloulklubed	pw	7.04	134.26	77.00	94	8.77	90
1107	sorland	no	67.67	12.69	44.60	87	11.41	75
1109	salalah	om	17.01	54.10	82.40	88	4.70	20
1110	hami	cn	42.84	93.51	55.48	37	4.52	12
1111	wanning	cn	18.80	110.40	84.01	100	6.20	8
1114	puerto ayora	ec	-0.74	-90.35	80.60	65	17.22	20
1118	srednekolymsk	ru	67.46	153.71	47.92	67	3.18	0
1119	castro	cl	-42.48	-73.76	46.75	97	6.20	48
1121	deulgaon raja	in	20.02	76.04	81.67	47	12.24	0
1122	chongwe	zm	-15.33	28.68	57.37	74	4.74	0
1126	salta	ar	-24.79	-65.41	77.00	47	9.17	0
1128	manacapuru	br	-3.30	-60.62	84.91	71	5.19	36
1132	georgetown	sh	-7.93	-14.42	79.15	100	15.48	0
1134	osorno	cl	-40.57	-73.14	51.80	71	5.82	75
1138	ostrovnoy	ru	68.05	39.51	33.97	100	16.26	92
1142	udachnyy	ru	66.42	112.40	36.40	73	4.41	56
1144	zhigansk	ru	66.77	123.37	39.64	63	4.97	24
1145	cabo san lucas	mx	22.89	-109.91	95.00	37	11.41	5
1147	semirom	ir	31.41	51.57	46.84	88	2.06	0
1148	ahipara	nz	-35.17	173.16	46.21	100	2.84	0
508 rows × 8 columns

#Get Data for Plots

global_weather_data_df = pd.DataFrame(no_dups_cities_list_df2)
global_weather_data_df.index= range(len(global_weather_data_df.index))
global_weather_data_df.to_csv("global_cities_weather_plt_data.csv")
# Create a path to the Global City Weather Data csv and read it into a Pandas DataFrame
csv_path = "global_cities_weather_plt_data.csv"
global_cities_weather_plt_data_df = pd.read_csv(csv_path)


with open(csv_path, newline='') as csvfile:

    # CSV reader specifies delimiter and variable that holds contents
    csvreader = csv.reader(csv_path, delimiter=',')
    # Skip the Headers
    next(csvreader,None)
global_cities_weather_plt_data_df

<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }
.dataframe tbody tr th {
    vertical-align: top;
}

.dataframe thead th {
    text-align: right;
}
</style>
Unnamed: 0	City	Country	Latitude	Longitude	Temperature	Humidity	Wind Speed	Cloudiness
0	0	yarmouth	ca	43.84	-66.12	64.40	72	9.17	20
1	1	punta arenas	cl	-53.16	-70.91	37.40	64	24.16	20
2	2	rikitea	pf	-23.12	-134.97	76.28	100	10.76	80
3	3	dikson	ru	73.51	80.55	29.66	97	3.38	88
4	4	kavieng	pg	-2.57	150.80	83.57	100	2.59	76
5	5	albany	au	-35.02	117.88	53.78	86	7.63	68
6	6	hermiston	us	45.84	-119.29	73.40	28	11.41	1
7	7	wanning	cn	18.80	110.40	84.47	100	7.52	32
8	8	port blair	in	11.67	92.75	80.96	100	13.33	80
9	9	san andres	co	8.22	-76.58	83.03	100	3.71	24
10	10	cape town	za	-33.93	18.42	50.00	93	6.93	0
11	11	khatanga	ru	71.98	102.47	29.03	81	3.27	0
12	12	saint-augustin	ca	45.63	-73.98	75.20	36	9.17	20
13	13	kapaa	us	22.08	-159.32	77.00	88	13.87	75
14	14	fortuna	us	40.60	-124.16	53.60	81	11.41	75
15	15	ushuaia	ar	-54.81	-68.31	37.40	80	24.16	40
16	16	yumen	cn	40.29	97.04	50.36	61	3.15	0
17	17	bethel	us	60.79	-161.76	44.60	75	14.99	90
18	18	georgetown	sh	-7.93	-14.42	79.34	100	14.23	8
19	19	esperance	au	-33.86	121.89	45.41	68	9.75	0
20	20	longyearbyen	sj	78.22	15.64	35.60	74	11.41	75
21	21	barrow	us	39.51	-90.40	80.60	69	3.36	40
22	22	east london	za	-33.02	27.91	55.40	71	9.17	0
23	23	palaikastron	gr	35.20	26.25	71.51	95	21.16	0
24	24	port elizabeth	za	-33.92	25.57	57.20	76	14.99	0
25	25	yellowknife	ca	62.45	-114.38	50.00	34	19.46	20
26	26	ribeira grande	pt	38.52	-28.70	68.00	64	16.11	40
27	27	cherskiy	ru	68.75	161.30	41.99	73	4.72	92
28	28	guerrero negro	mx	27.97	-114.04	65.21	78	9.86	24
29	29	mar del plata	ar	-46.43	-67.52	49.73	60	13.89	0
...	...	...	...	...	...	...	...	...	...
478	478	luanda	ao	-8.83	13.24	73.03	98	4.97	0
479	479	yellowknife	ca	62.45	-114.38	51.80	37	20.80	20
480	480	new norfolk	au	-42.78	147.06	44.60	70	8.05	20
481	481	matara	lk	5.95	80.54	80.14	96	18.95	76
482	482	doctor cecilio baez	py	-25.05	-56.32	78.07	45	6.64	0
483	483	rawson	ar	-43.30	-65.11	49.99	41	6.42	12
484	484	jacutinga	br	-22.29	-46.61	68.00	45	6.93	20
485	485	bahia honda	cu	22.90	-83.16	73.21	100	4.63	92
486	486	hasaki	jp	35.73	140.83	66.20	93	1.12	75
487	487	haimen	cn	31.87	121.18	71.50	90	6.08	100
488	488	kloulklubed	pw	7.04	134.26	77.00	94	8.77	90
489	489	sorland	no	67.67	12.69	44.60	87	11.41	75
490	490	salalah	om	17.01	54.10	82.40	88	4.70	20
491	491	hami	cn	42.84	93.51	55.48	37	4.52	12
492	492	wanning	cn	18.80	110.40	84.01	100	6.20	8
493	493	puerto ayora	ec	-0.74	-90.35	80.60	65	17.22	20
494	494	srednekolymsk	ru	67.46	153.71	47.92	67	3.18	0
495	495	castro	cl	-42.48	-73.76	46.75	97	6.20	48
496	496	deulgaon raja	in	20.02	76.04	81.67	47	12.24	0
497	497	chongwe	zm	-15.33	28.68	57.37	74	4.74	0
498	498	salta	ar	-24.79	-65.41	77.00	47	9.17	0
499	499	manacapuru	br	-3.30	-60.62	84.91	71	5.19	36
500	500	georgetown	sh	-7.93	-14.42	79.15	100	15.48	0
501	501	osorno	cl	-40.57	-73.14	51.80	71	5.82	75
502	502	ostrovnoy	ru	68.05	39.51	33.97	100	16.26	92
503	503	udachnyy	ru	66.42	112.40	36.40	73	4.41	56
504	504	zhigansk	ru	66.77	123.37	39.64	63	4.97	24
505	505	cabo san lucas	mx	22.89	-109.91	95.00	37	11.41	5
506	506	semirom	ir	31.41	51.57	46.84	88	2.06	0
507	507	ahipara	nz	-35.17	173.16	46.21	100	2.84	0
508 rows × 9 columns

#Temperature vs Latitude

sns.lmplot('Latitude', 'Temperature', data = global_cities_weather_plt_data_df)
plt.title("Temperature vs Latitude - (05/29/2018)")
plt.xlabel("Latitude")
plt.ylabel("Temperature")
plt.axvline(0, color="black",alpha=0.5)
plt.savefig("Temperature vs Latitude")
plt.show()
png

#Humidity vs Latitude

sns.lmplot('Latitude', 'Temperature', data = global_cities_weather_plt_data_df)
plt.title("Humidity vs Latitude- (05/29/2018)")
plt.xlabel("Latitude")
plt.ylabel("Humidity")
plt.axvline(0, color="black",alpha=0.5)
plt.savefig("Humidity vs Latitude")
plt.show()
png

#Cloudiness vs Latitude

sns.lmplot('Latitude', 'Cloudiness', data = global_cities_weather_plt_data_df)
plt.title("Cloudiness vs Latitude- (05/29/2018)")
plt.xlabel("Latitude")
plt.ylabel("Cloudiness")
plt.axvline(0, color="black",alpha=0.5)
plt.savefig("Cloudiness vs Latitude")
plt.show()
png

#Wind Speed vs Latitude

sns.lmplot('Latitude', 'Wind Speed', data = global_cities_weather_plt_data_df)
plt.title("Wind Speed vs Latitude- (05/29/2018)")
plt.xlabel("Latitude")
plt.ylabel("Wind Speed")
plt.axvline(0, color="black",alpha=0.5)
plt.savefig("Wind Speed vs Latitude")
plt.show()
png

© 2018 GitHub, Inc.
Terms
Privacy
Security
Status
Help
Contact GitHub
API
Training
Shop
Blog
About
