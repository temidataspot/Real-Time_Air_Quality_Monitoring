# Step 1: API Query for Real-Time Air Quality Data

# Objective
Use an API to query real-time air quality data for multiple cities across the globe.

# Tools
API: OpenWeatherMap API (Air Pollution endpoint)
Python for API querying
SQL for data storage

```python
import requests
import pandas as pd
import mysql.connector

# API key 
api_key = "API_KEY"

# Define cities to query
cities = ["New York", "London", "Delhi", "Beijing", "Sydney"]
url = "http://api.openweathermap.org/data/2.5/air_pollution"

# data
data_list = []

for city in cities:
    # Geocoding API to get latitude and longitude of the city
    geo_url = f"http://api.openweathermap.org/geo/1.0/direct?q={city}&appid={api_key}"
    geo_data = requests.get(geo_url).json()
    lat, lon = geo_data[0]['lat'], geo_data[0]['lon']
    
    # Query Air Pollution API
    response = requests.get(f"{url}?lat={lat}&lon={lon}&appid={api_key}")
    pollution_data = response.json()
    
    # Extract relevant data
    data_list.append({
        "city": city,
        "lat": lat,
        "lon": lon,
        "aqi": pollution_data["list"][0]["main"]["aqi"],  # Air Quality Index (1 to 5 scale)
        "pm2_5": pollution_data["list"][0]["components"]["pm2_5"],  # Fine particles
        "pm10": pollution_data["list"][0]["components"]["pm10"],  # Coarse particles
        "no2": pollution_data["list"][0]["components"]["no2"],  # Nitrogen dioxide
        "o3": pollution_data["list"][0]["components"]["o3"],  # Ozone
        "timestamp": pollution_data["list"][0]["dt"]  # Unix timestamp
    })

# Convert to DataFrame for better visualization
df = pd.DataFrame(data_list)
print(df)
```
# Output Snippet:

```
        city        lat        lon  aqi  pm2_5  pm10    no2    o3   timestamp
0   New York   40.7128   -74.0060    2   12.5   30.0   18.0   34.0   1695198000
1    London    51.5074   -0.1278     3   25.0   50.0   22.5   40.1   1695198600
2     Delhi    28.6139    77.2090    4   75.5  100.0   50.0   20.5   1695199200
3   Beijing    39.9042   116.4074    5  120.0  150.0   40.0   55.0   1695200000
4    Sydney   -33.8688   151.2093    2   10.5   20.0   12.5   60.2   1695200600
```
