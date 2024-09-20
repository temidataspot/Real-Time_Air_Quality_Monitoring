# 3: Cleaning, Forecasting, and Analysis in R

# Objective
Clean the air quality data and analyze trends, correlations, and outliers using R.

# Tools
R for data cleaning and exploratory data analysis (EDA)

```r
# Load necessary libraries
library(DBI)
library(dplyr)
library(ggplot2)

# Connect to MySQL database
con <- dbConnect(RMySQL::MySQL(), dbname = "air_quality_db", host = "localhost", 
                 user = "root", password = "password")

# Query the air quality data
air_quality_data <- dbGetQuery(con, "SELECT * FROM air_quality")

# Convert timestamp to human-readable format
air_quality_data$timestamp <- as.POSIXct(air_quality_data$timestamp, origin="1970-01-01", tz="UTC")

# Summary statistics to check for missing values
summary(air_quality_data)

# Basic Cleaning: Removing any potential missing values
cleaned_data <- air_quality_data %>%
  filter(!is.na(aqi) & !is.na(pm2_5) & !is.na(no2))

# Visualization: AQI comparison between cities
ggplot(cleaned_data, aes(x = city, y = aqi, fill = city)) +
  geom_bar(stat="identity") +
  theme_minimal() +
  labs(title = "Air Quality Index (AQI) Comparison Across Cities", x = "City", y = "AQI")
```
# Time-Series Forecasting 
```r
# Load time series forecasting libraries
library(forecast)

# Group data by city and prepare for time series analysis
air_quality_ts <- air_quality_data %>%
  group_by(city) %>%
  arrange(timestamp) %>%
  summarize(mean_aqi = mean(aqi))

# Convert data to time series object for a specific city
ts_data <- ts(air_quality_ts$mean_aqi, frequency = 365)

# Fit ARIMA model for forecasting
fit <- auto.arima(ts_data)

# Forecast for the next 30 days
forecasted_aqi <- forecast(fit, h = 30)

# Plot the forecast
plot(forecasted_aqi, main = "30-Day AQI Forecast")
