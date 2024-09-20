# Step 2: Import Data into SQL

# Objective
Store the air quality data in a SQL database for historical tracking and easier querying.

```sql
# Connecting to the SQL database
conn = mysql.connector.connect(
    host="localhost",
    user="root",
    password="password",
    database="air_quality_db"
)
cursor = conn.cursor()

# Creating the table if not exists
cursor.execute('''CREATE TABLE IF NOT EXISTS air_quality (
    id INT AUTO_INCREMENT PRIMARY KEY,
    city VARCHAR(255),
    lat FLOAT,
    lon FLOAT,
    aqi INT,
    pm2_5 FLOAT,
    pm10 FLOAT,
    no2 FLOAT,
    o3 FLOAT,
    timestamp INT
)''')

# Inserting data into the table
for index, row in df.iterrows():
    cursor.execute('''INSERT INTO air_quality (city, lat, lon, aqi, pm2_5, pm10, no2, o3, timestamp)
                      VALUES (%s, %s, %s, %s, %s, %s, %s, %s, %s)''',
                   (row['city'], row['lat'], row['lon'], row['aqi'], row['pm2_5'], row['pm10'], row['no2'], row['o3'], row['timestamp']))
conn.commit()
```
