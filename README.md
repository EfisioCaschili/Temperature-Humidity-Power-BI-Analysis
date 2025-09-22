# Temperature-Humidity-Power-BI-Analysis
## Project Description  

This repository contains the implementation of a data processing pipeline for sensor measurements, combining raw inputs from Excel files and a PostgreSQL database.  
The goal is to centralize and process heterogeneous data sources, ensuring clean, aggregated, and analysis-ready datasets for reporting and visualization in **Power BI**.  

---

### Data Pipeline  
- **Data ingestion**:  
  - Historical data imported from Excel spreadsheets.  
  - Continuous data acquisition stored in PostgreSQL (`samples` table).  

### Data Transformation  
- **Hourly aggregation**:  
  - Implemented through the SQL view `samples_hourly`.  
  - Uses `date_trunc('hour', timestamp)` and `AVG()` to resample raw data.  

- **Derivative calculation (Savitzy–Golay inspired)**:  
  - Implemented in the SQL view `misure_sg_hour`.  
  - Uses **window functions** (`LAG`, `LEAD`) to compute gradients of temperature values.  

---
### Integration with Power BI  
- Power BI is connected directly to PostgreSQL views.  
- No manual refresh required, since calculations are defined in SQL views.  
- Ensures that business intelligence dashboards always reflect the most recent sensor data.


---

## Example SQL Queries  

### Create Hourly Aggregated View  
```sql
CREATE OR REPLACE VIEW samples_hourly AS
SELECT
    sensor_id,
    date_trunc('hour', "timestamp") AS ts_hour,
    AVG(temperature) AS temperature
FROM samples
GROUP BY sensor_id, date_trunc('hour', "timestamp")
ORDER BY sensor_id, ts_hour;
---

### Create Derivative View
CREATE OR REPLACE VIEW misure_sg_hour AS
SELECT
    sensor_id,
    ts_hour AS timestamp,
    temperature,
    (
       (-2 * LAG(temperature,2)  OVER (PARTITION BY sensor_id ORDER BY ts_hour)) +
       (-1 * LAG(temperature,1)  OVER (PARTITION BY sensor_id ORDER BY ts_hour)) +
       ( 1 * LEAD(temperature,1) OVER (PARTITION BY sensor_id ORDER BY ts_hour)) +
       ( 2 * LEAD(temperature,2) OVER (PARTITION BY sensor_id ORDER BY ts_hour))
    ) / 3600.0 AS derivata_sg_hour
FROM samples_hourly
ORDER BY sensor_id, ts_hour;
```
---

## Requirements
### 
- PostgreSQL 14+
- Power BI Desktop / Service
- Access to raw Excel files with sensor data.
---
![Temp_Hum](https://github.com/user-attachments/assets/7016a7ec-542f-4322-b2c0-2142d67e9b31)
![TemperatureTrend](https://github.com/user-attachments/assets/b7a9f68d-0a56-4c41-abc7-877036ddb69f)
![OutOfRange](https://github.com/user-attachments/assets/370bc27f-ad00-40f0-a683-1b859c63d244)
![ChangeRate](https://github.com/user-attachments/assets/dccde160-dfd1-4066-98a6-c082cee78a71)
