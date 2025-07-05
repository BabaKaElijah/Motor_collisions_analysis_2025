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

### Run Queries
Use the provided SQL queries in this repository to explore and analyze the data.

### Extend Analysis
Add your own queries to uncover deeper insights or to tailor the analysis to specific needs.
