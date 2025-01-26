# Week 1: 

# Taxi Data Analysis Queries

## Q1: Docker and Python Environment
Run a Python 3.12.8 container and verify the `pip` version.

```bash
$ docker run -it python:3.12.8 bash
$ pip --version
pip 24.3.1 from /usr/local/lib/python3.12/site-packages/pip (python 3.12)
```

---

## Q2: PostgreSQL Configuration
- **User**: `postgres`
- **Port**: `5433`

---

## Q3: Trip Distance Distribution
Query to categorize trip distances within specified ranges for October 2019:

```sql
SELECT
	COUNT(*) FILTER (WHERE trip_distance <= 1) AS up_to_1_mile,
	COUNT(*) FILTER (WHERE trip_distance > 1 AND trip_distance <= 3) AS between_1_and_three_miles,
	COUNT(*) FILTER (WHERE trip_distance > 3 AND trip_distance <= 7) AS between_3_and_7_miles,
	COUNT(*) FILTER (WHERE trip_distance > 7 AND trip_distance <= 10) AS between_7_and_10_miles,
	COUNT(*) FILTER (WHERE trip_distance > 10) AS over_10_miles
FROM yellow_taxi_trips
WHERE lpep_pickup_datetime::date >= '2019-10-01' AND lpep_dropoff_datetime::date < '2019-11-01';
```

### Results:
- **Up to 1 mile**: 104,802
- **1 to 3 miles**: 198,924
- **3 to 7 miles**: 109,603
- **7 to 10 miles**: 27,678
- **Over 10 miles**: 35,189

---

## Q4: Maximum Trip Distance
Find the date and maximum trip distance for selected dates in October 2019:

```sql
SELECT
    DATE(lpep_pickup_datetime) AS pickup_date,
    MAX(trip_distance) AS max_trip_distance
FROM
    yellow_taxi_trips
WHERE
    DATE(lpep_pickup_datetime) IN ('2019-10-11', '2019-10-24', '2019-10-26', '2019-10-31')
GROUP BY
    DATE(lpep_pickup_datetime)
ORDER BY
    max_trip_distance DESC
LIMIT 1;
```

### Result:
- **Date**: 2019-10-31

---

## Q5: High Total Amounts by Pickup Location
Retrieve zones with a total amount greater than $13,000 for October 18, 2019:

```sql
SELECT DATE(lpep_pickup_datetime), "PULocationID", SUM(total_amount) AS total, zones."Zone"
FROM yellow_taxi_trips
INNER JOIN zones
ON "PULocationID" = "LocationID"
WHERE DATE(lpep_pickup_datetime) = '2019-10-18'
GROUP BY DATE(lpep_pickup_datetime), "PULocationID", zones."Zone"
HAVING SUM(total_amount) > 13000
LIMIT 3;
```

### Results:
1. **East Harlem North**
2. **East Harlem South**
3. **Morningside Heights**

---

## Q6: Maximum Tip for Dropoff Zones
Find the dropoff zone with the highest tip amount for pickups in **East Harlem North** during October 2019:

```sql
SELECT
    z_drop."Zone" AS dropoff_zone,
    MAX(ytt.tip_amount) AS max_tip
FROM
    yellow_taxi_trips ytt
INNER JOIN
    zones z_pickup ON ytt."PULocationID" = z_pickup."LocationID"
INNER JOIN
    zones z_drop ON ytt."DOLocationID" = z_drop."LocationID"
WHERE
    z_pickup."Zone" = 'East Harlem North'
    AND DATE(ytt.lpep_pickup_datetime) BETWEEN '2019-10-01' AND '2019-10-31'
GROUP BY
    z_drop."Zone"
ORDER BY
    max_tip DESC
LIMIT 1;
```

### Result:
- **Dropoff Zone**: JFK Airport

