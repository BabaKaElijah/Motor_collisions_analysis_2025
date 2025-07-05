# Motor Collisions Data Analysis

## Project Overview

This project involves analyzing a dataset of motor vehicle collisions to uncover insights about crash patterns, contributing factors, and vehicle characteristics. The dataset includes detailed information on each collision such as date, time, vehicle details, driver information, and damage reports.

The goal is to demonstrate practical SQL skills ranging from basic queries to advanced analytical techniques like window functions, CTEs, and views.

---

## Dataset Description

The dataset contains the following key fields:

| Column Name            | Description                                   |
|------------------------|-----------------------------------------------|
| unique_id              | Unique identifier for each collision record   |
| collision_id           | Collision event identifier                     |
| crash_date             | Date when the collision occurred               |
| crash_time             | Time when the collision occurred               |
| state_registration     | State or region vehicle was registered in      |
| vehicle_type           | Type of vehicle involved                        |
| vehicle_make           | Manufacturer of the vehicle                     |
| travel_direction       | Direction the vehicle was traveling             |
| vehicle_occupants      | Number of occupants in the vehicle              |
| driver_sex             | Driver gender (M/F)                             |
| driver_license_status  | Driver license status (licensed/unlicensed)    |
| pre_crash              | Vehicle action prior to crash (e.g., merging)  |
| point_of_impact        | Location on vehicle where impact occurred      |
| vehicle_damage         | Description of vehicle damage                    |
| public_property_damage | Description of public property damage           |
| contributing_factor    | Factors contributing to the collision          |

---

## SQL Analysis Highlights

- **Basic Queries**: Total collisions, counts by vehicle type, license status, and dates.
- **Aggregations & Groupings**: Collisions by state, vehicle make, and contributing factors.
- **Advanced Techniques**:  
  - Window functions (`RANK()`, `COUNT() OVER (PARTITION BY)`)  
  - Common Table Expressions (CTEs)  
  - CASE statements for severity classification  
  - Views for reusable summarized data

---

## Table Structure

The data is stored in a SQL Server table with the following structure:

```sql
CREATE TABLE collisions (
    unique_id INT IDENTITY(1,1) PRIMARY KEY,
    collision_id VARCHAR(50),
    crash_date DATE,
    crash_time TIME,
    state_registration CHAR(2),
    vehicle_type VARCHAR(50),
    vehicle_make VARCHAR(50),
    travel_direction VARCHAR(50),
    vehicle_occupants INT,
    driver_sex CHAR(1),
    driver_license_status VARCHAR(20),
    pre_crash VARCHAR(50),
    point_of_impact VARCHAR(100),
    vehicle_damage VARCHAR(100),
    public_property_damage VARCHAR(100),
    contributing_factor VARCHAR(100)
);
```
## How to Use
1. ### Import the Dataset
Clean and import your CSV dataset into the collisions table using SQL Server tools or scripts.

2. ### Run Queries
Use the provided SQL queries in this repository to explore and analyze the data.

3. ### Extend Analysis
Add your own queries to uncover deeper insights or to tailor the analysis to specific needs.

## Analysis Questions
### Basic Analysis
1. How many collisions occurred in total?
```sql
SELECT 
COUNT(*) AS total_collions
FROM Motor_Vehicle_Collisions_Vehicles
```
2. How many collisions involved unlicensed drivers?
```sql
SELECT 
DRIVER_LICENSE_STATUS,
COUNT(*) AS total_count
FROM Motor_Vehicle_Collisions_Vehicles
GROUP BY DRIVER_LICENSE_STATUS
```
3. List the number of collisions per vehicle type.
```sql
SELECT
VEHICLE_TYPE,
COUNT(*) AS total_collisions
FROM Motor_Vehicle_Collisions_Vehicles
GROUP BY VEHICLE_TYPE
ORDER BY total_collisions DESC;
```
4. Show the top 5 contributing factors to collisions.
```sql
SELECT TOP 5
CONTRIBUTING_FACTOR_1,
COUNT(*) AS total_collisions
FROM Motor_Vehicle_Collisions_Vehicles
GROUP BY CONTRIBUTING_FACTOR_1
ORDER BY total_collisions DESC;
```
5. Count how many collisions occurred in each state.
```sql
SELECT 
STATE_REGISTRATION,
COUNT(*) AS total_collisions
FROM Motor_Vehicle_Collisions_Vehicles
GROUP BY STATE_REGISTRATION
ORDER BY total_collisions DESC;
```
6. How many collisions happened when the vehicle was parked?
```sql
SELECT
COUNT(*) AS parked_collisions
FROM Motor_Vehicle_Collisions_Vehicles
WHERE PRE_CRASH = 'parked';
```
7. List all distinct vehicle makes involved in collisions.
```sql
SELECT DISTINCT UPPER(LTRIM(RTRIM(VEHICLE_MAKE))) AS cleaned_vehicle_make
FROM Motor_Vehicle_Collisions_Vehicles
WHERE VEHICLE_MAKE IS NOT NULL AND VEHICLE_MAKE <> '????' AND VEHICLE_MAKE <> '0'
ORDER BY cleaned_vehicle_make;
```
8. Find the average number of vehicle occupants per collision.
```sql
SELECT 
AVG(VEHICLE_OCCUPANTS) AS AVG_OCCUPANTS
FROM Motor_Vehicle_Collisions_Vehicles
```
9. List all collisions that occurred on a specific date (e.g., '2025-07-05').
```sql
SELECT 
COUNT(*) AS total_collisions
FROM Motor_Vehicle_Collisions_Vehicles
WHERE CRASH_DATE = '2015-10-02'
```
10. Show all records where public property damage occurred.
```sql
SELECT 
*
FROM Motor_Vehicle_Collisions_Vehicles
WHERE PUBLIC_PROPERTY_DAMAGE IS NOT NULL;
```
### Advanced Analysis
1. Rank the top 5 states by number of collisions.
```sql
SELECT TOP 5
STATE_REGISTRATION,
COUNT(*) AS total_collisions
FROM Motor_Vehicle_Collisions_Vehicles
WHERE STATE_REGISTRATION IS NOT NULL
GROUP BY STATE_REGISTRATION
ORDER BY total_collisions DESC;
```
2. Identify the hour of the day with the highest number of collisions.
```sql
SELECT
DATEPART(HOUR, CRASH_TIME) AS crash_hour,
COUNT(*) AS total_collisions
FROM Motor_Vehicle_Collisions_Vehicles
GROUP BY DATEPART(HOUR, CRASH_TIME)
ORDER BY total_collisions DESC;
```
3. Find collisions with more than average occupants involved.
```sql
SELECT *
FROM Motor_Vehicle_Collisions_Vehicles
WHERE vehicle_occupants > (
    SELECT AVG(CAST(vehicle_occupants AS FLOAT)) FROM Motor_Vehicle_Collisions_Vehicles
);
```
4. Create a severity indicator (e.g., CASE: high if property damage + impact front, etc.)
```sql
SELECT 
    UNIQUE_ID,
    COLLISION_ID,
    PUBLIC_PROPERTY_DAMAGE,
    POINT_OF_IMPACT,
    VEHICLE_DAMAGE,
    CASE
        WHEN PUBLIC_PROPERTY_DAMAGE IS NOT NULL AND POINT_OF_IMPACT LIKE '%front%' THEN 'High'
        WHEN POINT_OF_IMPACT LIKE '%center%' THEN 'Medium'
        ELSE 'Low'
    END AS severity_level
FROM Motor_Vehicle_Collisions_Vehicles;

--Using Partition to group all the severity levels
SELECT 
    unique_id,
    collision_id,
    public_property_damage,
    point_of_impact,
    vehicle_damage,
    -- Assign severity level
    CASE
        WHEN public_property_damage IS NOT NULL AND point_of_impact LIKE '%front%' THEN 'High'
        WHEN point_of_impact LIKE '%rear%' THEN 'Medium'
        ELSE 'Low'
    END AS severity_level,
    
    -- Count total collisions per severity level
    COUNT(*) OVER (
        PARTITION BY 
            CASE
                WHEN public_property_damage IS NOT NULL AND point_of_impact LIKE '%front%' THEN 'High'
                WHEN point_of_impact LIKE '%rear%' THEN 'Medium'
                ELSE 'Low'
            END
    ) AS total_in_severity_group

FROM Motor_Vehicle_Collisions_Vehicles;
```
5. List the top 3 most common travel directions per vehicle type.
```sql
SELECT *
FROM (
    SELECT 
        vehicle_type,
        travel_direction,
        COUNT(*) AS total_collisions,
        RANK() OVER (
            PARTITION BY vehicle_type
            ORDER BY COUNT(*) DESC
        ) AS direction_rank
    FROM Motor_Vehicle_Collisions_Vehicles
    GROUP BY vehicle_type, travel_direction
) AS ranked_directions
WHERE direction_rank <= 3
ORDER BY vehicle_type, direction_rank;
```
6. Show the percentage of male vs female drivers.
```sql
SELECT 
    driver_sex,
    COUNT(*) AS total_drivers,
    ROUND(
        COUNT(*) * 100.0 / SUM(COUNT(*)) OVER (), 
        2
    ) AS percentage_of_total
FROM Motor_Vehicle_Collisions_Vehicles
WHERE driver_sex IN ('M', 'F')
GROUP BY driver_sex
ORDER BY percentage_of_total DESC;
```
7. Detect duplicate entries (same collision_id, crash_date, vehicle_make).
```sql
SELECT 
    collision_id, 
    crash_date, 
    vehicle_make, 
    COUNT(*) AS occurrences
FROM Motor_Vehicle_Collisions_Vehicles
GROUP BY collision_id, crash_date, vehicle_make
HAVING COUNT(*) > 1
ORDER BY occurrences DESC;
```
8. Using a CTE, get the top 5 contributing factors excluding 'Unknown'.
```sql
WITH TopFactors AS (
    SELECT contributing_factor_1, COUNT(*) AS total_collisions
    FROM Motor_Vehicle_Collisions_Vehicles
    WHERE contributing_factor_1 <> 'Unknown'
    GROUP BY contributing_factor_1
)
SELECT TOP 5 *
FROM TopFactors
ORDER BY total_collisions DESC;
```
9. Find vehicles that were merging and hit in the left front bumper.
```sql
SELECT 
COLLISION_ID,
PRE_CRASH,
POINT_OF_IMPACT
FROM Motor_Vehicle_Collisions_Vehicles
WHERE PRE_CRASH = 'Merging' AND POINT_OF_IMPACT = 'Left Front Bumper'
```
10. Create a view that summarizes collisions by vehicle make, showing totals and average occupants.
```sql
CREATE VIEW v_CollisionSummary
AS
SELECT 
    vehicle_make,
    COUNT(*) AS total_collisions,
    AVG(CAST(vehicle_occupants AS FLOAT)) AS avg_occupants
FROM Motor_Vehicle_Collisions_Vehicles
GROUP BY vehicle_make;

SELECT * FROM v_CollisionSummary
```
## Skills Demonstrated
- SQL querying (basic to advanced)
- Data cleaning and preparation
- Analytical thinking and problem solving
- Use of SQL Server features such as window functions, CTEs, and views

## Author
Ellias Sithole
