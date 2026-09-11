# ASG Airlines – End-to-End Data Engineering & Analytics Pipeline
this is for neostats placement round usecase study documentation submission


## Project Overview

This project implements an end-to-end data engineering and analytics pipeline for **ASG Airlines**.

The pipeline takes raw airline operational data from multiple sources, performs data quality checks and cleaning, standardizes the data, applies transformations, creates analytical datasets, and connects the processed data to **Power BI** for interactive business intelligence and KPI analysis.

The project focuses on:

* Data ingestion
* Data cleaning and validation
* Data standardization
* Data transformation
* PII protection
* Analytical data modeling
* KPI generation
* Power BI dashboard development
* Data quality and anomaly analysis

---

## Business Problem

ASG Airlines maintains operational data related to:

* Flights
* Bookings
* Passengers
* Payments

The raw dataset contains intentional data-quality challenges such as:

* Missing values
* Inconsistent text formats
* Potentially malformed flight IDs
* Different timestamp formats
* Overnight flights
* Inconsistent or missing booking/payment information

The objective is to transform this raw data into a reliable analytical dataset that can support operational and business decision-making.

---

## Objectives

The main objectives of this project are:

1. Ingest raw airline data.
2. Profile the datasets and identify data-quality issues.
3. Clean and standardize the data.
4. Validate flight IDs and timestamps.
5. Calculate flight duration from departure and arrival timestamps.
6. Identify overnight flights.
7. Standardize airline and airport information.
8. Perform referential integrity checks.
9. Protect personally identifiable information (PII).
10. Build analytical fact and dimension datasets.
11. Calculate airline and route-level KPIs.
12. Develop an interactive Power BI dashboard.
13. Document the complete data pipeline and assumptions.

---

## Dataset

The source Excel workbook contains four datasets.

| Dataset    |  Rows | Columns | Description                                   |
| ---------- | ----: | ------: | --------------------------------------------- |
| Flights    | 1,020 |       7 | Flight and schedule information               |
| Bookings   | 1,000 |       9 | Passenger flight booking information          |
| Passengers | 1,039 |       9 | Passenger demographic and contact information |
| Payments   | 1,000 |       4 | Booking payment information                   |

### Flights

Important columns include:

* `flight_id`
* `airline`
* `source`
* `destination`
* `departure_time`
* `arrival_time`
* `duration`

### Bookings

Important columns include:

* `booking_id`
* `passenger_id`
* `flight_id`
* `booking_date`
* `status`
* `passport_number`
* `seat_number`

### Passengers

Important columns include:

* `passenger_id`
* `first_name`
* `last_name`
* `age`
* `gender`
* `email`
* `phone`
* `aadhaar_id`
* `date_of_birth`

### Payments

Important columns include:

* `payment_id`
* `booking_id`
* `amount`
* `payment_method`

---

## Technology Stack

| Technology             | Purpose                                |
| ---------------------- | -------------------------------------- |
| Python                 | Data engineering and transformation    |
| Pandas                 | Data processing                        |
| NumPy                  | Numerical operations                   |
| Jupyter / Google Colab | Pipeline development                   |
| Excel                  | Source data                            |
| Power BI               | Dashboard and visualization            |
| DAX                    | KPI calculations                       |
| SQL                    | Analytical queries and modeling        |
| GitHub                 | Version control and project repository |
| Draw.io                | Architecture and data-flow diagrams    |
| Microsoft Word         | Project documentation                  |

---

## Architecture

The project follows a layered data pipeline:

```text
RAW DATA
    |
    v
DATA INGESTION
    |
    v
DATA QUALITY & CLEANING
    |
    v
TRANSFORMATION
    |
    v
ANALYTICAL DATA LAYER
    |
    v
POWER BI
    |
    v
BUSINESS INSIGHTS / KPIs
```

### Pipeline Layers

**1. Raw Data**

Original airline datasets received from the source Excel workbook.

**2. Data Ingestion**

The Excel workbook is loaded using Python and Pandas.

**3. Data Quality & Cleaning**

Missing values, invalid formats, duplicate records, inconsistent text values and invalid timestamps are identified and handled.

**4. Transformation**

Flight duration, route information, date features and overnight-flight indicators are generated.

**5. Analytical Data Layer**

Cleaned data is organized into fact and dimension datasets suitable for analytics.

**6. Power BI**

The analytical datasets are imported into Power BI and connected using relationships.

**7. Business Insights / KPIs**

The dashboard provides airline, route, duration and data-quality insights.

---

## Data Cleaning Strategy

### 1. Missing Value Analysis

Missing values are identified for every dataset and column.

Examples include:

* Missing airline values
* Missing payment amounts
* Missing booking status
* Missing passenger last names

Missing values are handled according to the meaning of each field rather than applying the same method to every column.

---

### 2. Text Standardization

Airline and airport fields are standardized using:

* Leading/trailing whitespace removal
* Uppercase conversion
* Consistent string representation

Example:

```text
Air India
air india
 AIR INDIA
```

are standardized to:

```text
AIR INDIA
```

---

### 3. Flight ID Validation

Flight IDs are checked against a defined format pattern.

Example validation pattern:

```text
^[A-Z0-9]{5}$
```

Invalid records are flagged instead of being silently removed.

---

### 4. Timestamp Standardization

Departure and arrival timestamps are converted into a consistent datetime format.

Invalid timestamps are converted to missing values and flagged for data-quality review.

---

### 5. Flight Duration

Flight duration is calculated from the timestamps:

```text
Flight Duration =
Arrival Time - Departure Time
```

The calculated duration is stored in:

* Minutes
* Hours

The calculated value can also be compared with the original duration field to identify inconsistencies.

---

### 6. Overnight Flight Handling

A flight is classified as overnight when the arrival date is later than the departure date.

Example:

```text
Departure: 2026-04-20 23:38
Arrival:   2026-04-21 02:32
```

This is classified as:

```text
Overnight Flight = TRUE
```

This approach correctly handles flights crossing midnight.

---

### 7. Route Creation

A standardized route field is created:

```text
SOURCE → DESTINATION
```

Example:

```text
BOM → CCU
```

This allows route-level analysis in Power BI.

---

## PII Protection

The passenger dataset contains personally identifiable information.

Sensitive fields include:

* Passenger names
* Email addresses
* Phone numbers
* Aadhaar numbers
* Date of birth
* Passport numbers
* Emergency contact information

These fields should not be exposed in public analytical datasets.

### Protection Strategy

For analytics:

* Passenger identifiers are hashed where required.
* Aadhaar values can be masked.
* Raw personal contact information is excluded from analytical datasets.
* Sensitive source files should not be committed to a public GitHub repository.

Example:

```text
Original Aadhaar:
123456789012

Masked:
********9012
```

The analytical dataset should contain only the minimum information required for analysis.

---

## Analytical Data Model

The analytical layer follows a fact-and-dimension approach.

### Dimension Tables

```text
DIM_AIRLINE
    |
    | 1 : *
    v
FACT_FLIGHTS
```

```text
DIM_ROUTE
    |
    | 1 : *
    v
FACT_FLIGHTS
```

```text
DIM_PASSENGER
    |
    | 1 : *
    v
FACT_BOOKINGS
```

### Fact Tables

```text
FACT_FLIGHTS
FACT_BOOKINGS
FACT_PAYMENTS
```

### Main Relationships

```text
DIM_AIRLINE
      |
      v
FACT_FLIGHTS
      |
      v
FACT_BOOKINGS
      |
      v
FACT_PAYMENTS

DIM_ROUTE
      |
      v
FACT_FLIGHTS

DIM_PASSENGER
      |
      v
FACT_BOOKINGS
```

---

## Analytical Datasets

The pipeline generates the following analytical datasets:

```text
fact_flights.csv
fact_bookings.csv
fact_payments.csv
dim_passenger.csv
dim_airline.csv
dim_route.csv
```

### Fact Flights

Contains:

* Flight ID
* Airline
* Source
* Destination
* Route
* Departure timestamp
* Arrival timestamp
* Departure date
* Arrival date
* Departure hour
* Arrival hour
* Departure day
* Duration in minutes
* Duration in hours
* Overnight indicator
* Data-quality flag

### Dimension Airline

Contains:

* Airline key
* Airline

### Dimension Route

Contains:

* Route key
* Source
* Destination
* Route

### Dimension Passenger

Contains only the minimum required analytical passenger information.

---

## Key Performance Indicators

The project calculates the following KPIs.

### 1. Total Flights

Number of flights available in the analytical dataset.

### 2. Total Airlines

Number of unique airlines.

### 3. Total Routes

Number of unique source-destination routes.

### 4. Average Flight Duration

Average flight duration in minutes.

### 5. Minimum Flight Duration

Shortest calculated flight duration.

### 6. Maximum Flight Duration

Longest calculated flight duration.

### 7. Overnight Flights

Number of flights where arrival occurs on the next calendar day.

### 8. Total Anomalies

Number of records containing data-quality issues.

### 9. Anomaly Rate

```text
Anomaly Rate =
Total Anomalies / Total Flights
```

### 10. Route Traffic

Number of flights operating on each route.

### 11. Airline Distribution

Number and percentage of flights operated by each airline.

---

## Important Data Limitation

The provided dataset contains departure and arrival timestamps, but it does not provide separate:

* Scheduled departure time
* Actual departure time
* Scheduled arrival time
* Actual arrival time

Therefore, actual flight delay minutes cannot be reliably calculated from the available data.

The project treats relevant data-quality issues as **anomalies** rather than claiming they are actual operational delays.

---

# Power BI Dashboard

The cleaned analytical datasets are loaded into Power BI.

The dashboard is divided into four main pages.

---

## Page 1 – Executive Overview

### KPI Cards

* Total Flights
* Total Airlines
* Total Routes
* Average Flight Duration
* Overnight Flights
* Total Anomalies

### Visuals

* Flight Distribution by Airline
* Top 10 Routes by Flight Count
* Average Flight Duration by Airline
* Overnight vs Non-Overnight Flights
* Flights by Departure Day

### Slicers

* Airline
* Route
* Source Airport
* Destination Airport

---

## Page 2 – Duration Analysis

### KPI Cards

* Average Duration
* Minimum Duration
* Maximum Duration

### Visuals

* Average Duration by Airline
* Average Duration by Route
* Average Duration by Departure Hour
* Overnight vs Non-Overnight Duration

### Slicers

* Airline
* Route

---

## Page 3 – Route Performance

### KPI Cards

* Total Flights
* Total Routes

### Visuals

* Top 10 Routes
* Flights by Source Airport
* Flights by Destination Airport
* Average Duration by Route
* Source-to-Destination Traffic Matrix

### Slicers

* Route
* Airline

---

## Page 4 – Data Quality & Anomaly Insights

### KPI Cards

* Total Flights
* Total Anomalies
* Valid Flights
* Anomaly Rate
* Invalid Flight IDs

### Visuals

* Flight Data Quality Status
* Anomalies by Airline
* Anomalies by Route
* Recorded vs Calculated Duration Difference
* Overnight Flight Distribution
* Flight Data Quality Details Table

### Slicers

* Airline
* Route
* Source
* Destination
* Data Quality Flag

---

# DAX Measures

Important Power BI measures include:

```DAX
Total Flights = COUNTROWS(fact_flights)
```

```DAX
Total Airlines = DISTINCTCOUNT(fact_flights[airline])
```

```DAX
Total Routes = DISTINCTCOUNT(fact_flights[route])
```

```DAX
Average Flight Duration =
AVERAGE(fact_flights[duration_minutes])
```

```DAX
Overnight Flights =
CALCULATE(
    COUNTROWS(fact_flights),
    fact_flights[is_overnight] = TRUE()
)
```

```DAX
Total Anomalies =
CALCULATE(
    COUNTROWS(fact_flights),
    fact_flights[data_quality_flag] <> "VALID"
)
```

```DAX
Minimum Flight Duration =
MIN(fact_flights[duration_minutes])
```

```DAX
Maximum Flight Duration =
MAX(fact_flights[duration_minutes])
```

```DAX
Valid Flights =
CALCULATE(
    COUNTROWS(fact_flights),
    fact_flights[data_quality_flag] = "VALID"
)
```

```DAX
Anomaly Rate =
DIVIDE(
    [Total Anomalies],
    [Total Flights],
    0
)
```

---

# Data Validation

The pipeline performs several validation checks.

### Flight Validation

* Flight ID format
* Missing airline
* Missing timestamps
* Negative duration
* Duration mismatch
* Overnight classification

### Booking Validation

* Booking ID uniqueness
* Flight ID referential integrity
* Passenger ID referential integrity
* Missing booking status

### Payment Validation

* Booking ID referential integrity
* Missing payment amount
* Payment method consistency

### Passenger Validation

* Passenger ID uniqueness
* Missing demographic fields
* PII protection

---


Additional production capabilities could include:

* Incremental data ingestion
* Automated scheduling
* Data quality monitoring
* Metadata management
* Data lineage
* Role-based access control
* Encryption
* Audit logging
* Automated anomaly detection
* Cloud-based data storage

---

# Data Governance

The project follows basic data-governance principles:

* Minimize exposure of PII.
* Mask or hash sensitive identifiers.
* Separate raw and analytical datasets.
* Validate data before analysis.
* Maintain data-quality flags.
* Avoid exposing raw passenger information in public repositories.
* Document assumptions and transformations.
* Maintain reproducibility through code and version control.

---

# Outputs

The final project produces:

### Data

```text
Cleaned CSV datasets
Analytical fact tables
Analytical dimension tables
KPI outputs
```

### Documentation

```text
Architecture Diagram
Data Flow Diagram
Data Model
Case Study Report
README
```

### Visualization

```text
Power BI Dashboard
Dashboard Screenshots
```

### Code

```text
Python Data Pipeline
SQL Queries
Jupyter / Colab Notebook
```


# Expected Business Insights

The dashboard can help ASG Airlines understand:

* Which airlines operate the most flights.
* Which routes have the highest traffic.
* Which airlines have longer average flight durations.
* How many flights operate overnight.
* Which routes have unusual duration patterns.
* The overall quality of the operational data.
* The distribution of data-quality anomalies.
* Airport-level traffic patterns.

These insights can support operational monitoring and future planning.

---

# Conclusion

The ASG Airlines Data Engineering project demonstrates a complete data pipeline from raw operational data to business intelligence.

The solution addresses data ingestion, data cleaning, standardization, transformation, PII protection, analytical modeling, KPI calculation and Power BI visualization.

The architecture is designed so that the current Python-based implementation can be extended to scalable cloud technologies such as Azure Data Factory, Azure Data Lake, Databricks, Synapse and Power BI.

The final result provides a structured and reusable foundation for airline operational analytics and data-driven decision-making.

---

## Author

SANTHOSH.S VIT VELLORE

**ASG Airlines – Data Engineering & Analytics Case Study**

Technologies: Python | Pandas | SQL | Power BI | GitHub | Draw.io
