# WeatherAnalysis
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Load the dataset
data = pd.read_csv('/mnt/data/weatherdataset.txt', delimiter='\t')



data.to_csv('weatherdataset.csv', index=False)

print("Dataset successfully converted to CSV!")


# Load the dataset
data = pd.read_csv('weatherdataset.txt', delimiter='\t')

# Ensure 'date' column is in datetime format
data['date'] = pd.to_datetime(data['date'], errors='coerce')

# Handle missing or erroneous values
data['precipitation'] = pd.to_numeric(data['precipitation'], errors='coerce')
data.fillna(0, inplace=True)

# Add month and year columns for seasonal analysis
data['month'] = data['date'].dt.month
data['year'] = data['date'].dt.year

# Seasonal Analysis: Average temperature by month
seasonal_temp = data.groupby('month')[['tmax', 'tmin', 'tavg']].mean()
plt.figure(figsize=(12, 6))
seasonal_temp.plot(kind='line', marker='o')
plt.title('Seasonal Temperature Patterns')
plt.xlabel('Month')
plt.ylabel('Temperature (°F)')
plt.legend(['Max Temp', 'Min Temp', 'Avg Temp'])
plt.grid()
plt.show()

# Rainfall Trend Analysis: Total rainfall by month
monthly_rainfall = data.groupby('month')['precipitation'].sum()
plt.figure(figsize=(12, 6))
monthly_rainfall.plot(kind='bar', color='skyblue')
plt.title('Monthly Rainfall Totals')
plt.xlabel('Month')
plt.ylabel('Total Precipitation (inches)')
plt.grid(axis='y')
plt.show()

# Correlation Heatmap
correlation_matrix = data[['tmax', 'tmin', 'tavg', 'precipitation']].corr()
plt.figure(figsize=(8, 6))
sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm', fmt='.2f')
plt.title('Correlation Heatmap')
plt.show()

# Time Series Visualization: Temperature and Rainfall over Time
plt.figure(figsize=(14, 7))
plt.plot(data['date'], data['tavg'], label='Avg Temperature', color='red')
plt.bar(data['date'], data['precipitation'], label='Precipitation', color='blue', alpha=0.3)
plt.title('Temperature and Rainfall Trends Over Time')
plt.xlabel('Date')
plt.ylabel('Values')
plt.legend()
plt.grid()
plt.show()

# Temperature Extremes: Identify the hottest and coldest days
hottest_day = data.loc[data['tmax'].idxmax()]
coldest_day = data.loc[data['tmin'].idxmin()]
print(f"Hottest Day: {hottest_day['date']} with Max Temp {hottest_day['tmax']}°F")
print(f"Coldest Day: {coldest_day['date']} with Min Temp {coldest_day['tmin']}°F")
