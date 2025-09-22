# Temperature-Humidity-Power-BI-Analysis
## Project Description  

This repository contains the implementation of a data processing pipeline for sensor measurements, combining raw inputs from Excel files and a PostgreSQL database.  

### Data Pipeline  
- **Data ingestion**:  
  - Historical data imported from Excel spreadsheets.  
  - Continuous data acquisition stored in PostgreSQL (`samples` table).  

- **Data transformation**:  
  - Creation of an **hourly aggregated view** (`samples_hourly`) using `date_trunc` and `AVG()` to resample raw data originally collected every 10 minutes.  
  - Definition of **derivative calculations** (`misure_sg_hour`) leveraging SQL **window functions** (`LAG`, `LEAD`) to compute numerical gradients of temperature values.  

### Integration with Power BI  
- Power BI is connected directly to PostgreSQL views.  
- No manual refresh required, since calculations are defined in SQL views.  
- Ensures that business intelligence dashboards always reflect the most recent sensor data.  
