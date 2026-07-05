# Uber Supply-Demand Gap Analysis

## Project Overview
This project analyzes Uber request data to identify supply-demand gaps, peak-hour shortages, cancellation patterns, and operational improvement opportunities.

## Tools Used
- Excel (Data Cleaning & Dashboarding)
- SQL (Business Insights)
- Python (Pandas, Matplotlib) for EDA

---

# 1. Data Cleaning Using Excel

### Remove Duplicates
Data → Remove Duplicates

### Handle Missing Values
- Missing Driver IDs → "Not Assigned"
- Missing Drop Timestamps retained for Cancelled and No Cars Available requests

### Trim Extra Spaces
```excel
=TRIM(B2)
```

### Standardize Date Format
```text
dd-mm-yyyy hh:mm:ss
```

### Create Derived Columns

#### Request Hour
```excel
=HOUR(E2)
```

#### Request Date
```excel
=INT(E2)
```

#### Day Name
```excel
=TEXT(E2,"dddd")
```

#### Time Slot
```excel
=IF(AND(HOUR(E2)>=5,HOUR(E2)<10),"Morning Peak",
IF(AND(HOUR(E2)>=17,HOUR(E2)<22),"Evening Peak",
IF(HOUR(E2)<5,"Late Night","Normal")))
```

#### Trip Duration
```excel
=(F2-E2)*1440
```

---

# 2. Excel Dashboard

## KPI Cards
- Total Requests
- Completed Trips
- Cancelled Trips
- No Cars Available
- Completion Rate
- Supply Gap %

## Visualizations
1. Status Distribution (Column Chart)
2. Requests by Hour (Line Chart)
3. Pickup Point vs Status (Stacked Column Chart)
4. Time Slot Analysis (Bar Chart)

## Slicers
- Pickup Point
- Status
- Time Slot
- Day Name

---

# 3. SQL Analysis

## Total Requests
```sql
SELECT COUNT(*) AS total_requests
FROM uber_requests;
```

## Status Distribution
```sql
SELECT status, COUNT(*) AS total_requests
FROM uber_requests
GROUP BY status;
```

## Pickup Point Analysis
```sql
SELECT pickup_point, COUNT(*) AS requests
FROM uber_requests
GROUP BY pickup_point;
```

## Hourly Demand
```sql
SELECT EXTRACT(HOUR FROM request_timestamp) AS request_hour,
       COUNT(*) AS total_requests
FROM uber_requests
GROUP BY request_hour
ORDER BY request_hour;
```

## Supply Gap
```sql
SELECT status, COUNT(*) AS total
FROM uber_requests
WHERE status IN ('Cancelled','No Cars Available')
GROUP BY status;
```

## Peak Hour Supply Gap
```sql
SELECT EXTRACT(HOUR FROM request_timestamp) AS hour,
       COUNT(*) AS gap_requests
FROM uber_requests
WHERE status IN ('Cancelled','No Cars Available')
GROUP BY hour
ORDER BY gap_requests DESC;
```

---

# 4. Pandas EDA

```python
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv('Uber Request Data.csv')

df.info()
df.describe(include='all')
df.isnull().sum()
```

## Status Distribution

```python
df['Status'].value_counts()
```

## Pickup Point Analysis

```python
pd.crosstab(df['Pickup point'], df['Status'])
```

## Datetime Conversion

```python
df['Request timestamp'] = pd.to_datetime(
    df['Request timestamp'],
    dayfirst=True
)
```

## Hourly Demand

```python
df['Request_Hour'] = df['Request timestamp'].dt.hour

hourly = df.groupby('Request_Hour')['Request id'].count()

hourly.plot()
plt.show()
```

## Supply Gap Analysis

```python
gap = df[df['Status'].isin(['Cancelled','No Cars Available'])]
```

---

# Key Business Insights

1. Morning Peak shows high trip cancellations.
2. Evening Peak has the highest "No Cars Available" cases.
3. Airport demand exceeds supply during evening hours.
4. Driver availability is insufficient during peak periods.
5. Optimizing driver allocation can reduce the supply-demand gap.

---

## Project Outcome

The analysis identifies demand hotspots, supply shortages, and operational opportunities that can improve ride completion rates and customer satisfaction.
