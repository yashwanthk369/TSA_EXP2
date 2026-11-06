# Ex.No: 02 LINEAR AND POLYNOMIAL TREND ESTIMATION
Date:
### AIM:
To Implement Linear and Polynomial Trend Estiamtion Using Python.

### DATASET 
sensor fault detection dataset 

### SOFTWARE REQUIREMENT 
Jupyter Notebook

### ALGORITHM:
Import necessary libraries (NumPy, Matplotlib)

Load the dataset

Calculate the linear trend values using least square method

Calculate the polynomial trend values using least square method

End the program
### PROGRAM:

```
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

df = pd.read_csv('sensor-fault-detection.csv', sep=';')

df.columns = df.columns.str.strip().str.lower()
print("Columns found:", df.columns)

df['timestamp'] = pd.to_datetime(df['timestamp'], errors='coerce', utc=True)
df = df.dropna(subset=['timestamp'])

if 'sensorid' in df.columns:
    sensor_id = 1
    df = df[df['sensorid'] == sensor_id].copy()
    print(f"Filtered for sensor ID: {sensor_id}")

df = df.sort_values('timestamp')
df = df.set_index('timestamp')

df_value = df[['value']].groupby(level=0).mean()
resampled_data = df_value.resample('M').mean()

resampled_data = resampled_data.dropna(subset=['value'])

resampled_data.reset_index(inplace=True)
resampled_data.rename(columns={'timestamp': 'Date'}, inplace=True)

n = len(resampled_data)
print(f"Number of monthly data points after resampling: {n}")

X = [i - n // 2 for i in range(n)]
price = resampled_data['value'].tolist()

if n < 2:
    print("Not enough data for trend analysis.")
else:
    x2 = [i**2 for i in X]
    xy = [i*j for i, j in zip(X, price)]

    b = (n*sum(xy) - sum(price)*sum(X)) / (n*sum(x2) - (sum(X)**2))
    a = (sum(price) - b*sum(X)) / n
    linear_trend = [a + b*X[i] for i in range(n)]

    print(f"Linear Trend: y = {a:.2f} + {b:.2f}x")
    resampled_data['Linear Trend'] = linear_trend

    if n >= 3:
        X_array = np.array(X, dtype=float)
        X_min, X_max = X_array.min(), X_array.max()
        X_scaled = 2 * (X_array - X_min) / (X_max - X_min) - 1 if X_max - X_min > 0 else X_array

        coeffs = np.polyfit(X_scaled, price, 2)
        a_poly, b_poly, c_poly = coeffs
        poly_trend = [a_poly*(X_scaled[i]**2) + b_poly*X_scaled[i] + c_poly for i in range(n)]

        print(f"Polynomial Trend (Scaled): y = {a_poly:.2f}x² + {b_poly:.2f}x + {c_poly:.2f}")
        resampled_data['Polynomial Trend'] = poly_trend

    resampled_data.set_index('Date', inplace=True)

    plt.figure(figsize=(12, 6))
    resampled_data['value'].plot(marker='o', label='Actual Sensor Value (Monthly Mean)')
    resampled_data['Linear Trend'].plot(linestyle='--', label='Linear Trend')

    if 'Polynomial Trend' in resampled_data.columns:
        resampled_data['Polynomial Trend'].plot(marker='s', label='Polynomial Trend')

    plt.title('Sensor Value Monthly Trend Analysis (Sensor ID: 1)')
    plt.xlabel('Date')
    plt.ylabel('Sensor Value')
    plt.legend()
    plt.grid(True)
    plt.show()
```

### OUTPUT

<img width="1327" height="777" alt="image" src="https://github.com/user-attachments/assets/504539d2-4268-4ec4-b746-1c12b8f0eeb1" />

### RESULT:
Thus the python program for linear and Polynomial Trend Estiamtion has been executed successfully.
