# 

<h2>Q1</h2>
docker run -it python:3.12.8 bash 
pip --version 
pip 24.3.1 from /usr/local/lib/python3.12/site-packages/pip (python 3.12)

<h2>Q2</h2>
user - postgres
port - 5433 

<h2>Q3</h2>
SELECT 
	COUNT(*) FILTER (WHERE trip_distance <= 1) as up_to_1_mile, 
	COUNT(*) FILTER (WHERE trip_distance > 1 AND trip_distance <= 3) as between_1_and_three_miles, 
	COUNT(*) FILTER (WHERE trip_distance > 3 AND trip_distance <= 7) as between_3_and_7_miles,
	COUNT(*) FILTER (WHERE trip_distance > 7 AND trip_distance <= 10) as between_7_and_10_miles,
	COUNT(*) FILTER (WHERE trip_distance > 10) as over_10_miles
FROM yellow_taxi_trips
WHERE lpep_pickup_datetime::date >= '2019-10-01' and lpep_dropoff_datetime::date < '2019-11-01';		
	
a: 104,802; 198,924; 109,603; 27,678; 35,189

<h2>Q4</h2>
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

2019-10-31

<h2>Q5</h2>
SELECT date(lpep_pickup_datetime), "PULocationID", SUM(total_amount) AS total, zones."Zone"
FROM yellow_taxi_trips 
INNER JOIN zones
ON "PULocationID" = "LocationID"
WHERE date(lpep_pickup_datetime) = '2019-10-18'
GROUP BY date(lpep_pickup_datetime), "PULocationID", zones."Zone"
HAVING SUM(total_amount) > 13000
LIMIT 3;

east harlem north - east harlem south - morningside heights 

<h2>Q6</h2>
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
    AND date(ytt.lpep_pickup_datetime) BETWEEN '2019-10-01' AND '2019-10-31'
GROUP BY 
    z_drop."Zone"
ORDER BY 
    max_tip DESC
LIMIT 1;

- JFK Airport 