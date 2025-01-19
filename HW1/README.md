--Question 3
SELECT
SUM(CASE WHEN trip_distance <= 1 THEN 1 ELSE 0 END) AS upto_1_mile,
SUM(CASE WHEN trip_distance > 1 AND trip_distance <=3 THEN 1 ELSE 0 END) AS bwn_1_and_3_mile,
SUM(CASE WHEN trip_distance > 3 AND trip_distance <=7 THEN 1 ELSE 0 END) AS bwn_3_and_7_mile,
SUM(CASE WHEN trip_distance > 7 AND trip_distance <=10 THEN 1 ELSE 0 END) AS bwn_7_and_10_mile,
SUM(CASE WHEN trip_distance > 10 THEN 1 ELSE 0 END) AS over_10_miles
FROM green_taxi_data 
WHERE
lpep_dropoff_datetime >= '2019-10-01' 
    AND lpep_dropoff_datetime < '2019-11-01';

--Question 4
SELECT
	MAX(trip_distance),
	DATE(lpep_pickup_datetime)
FROM green_taxi_data
WHERE DATE(lpep_pickup_datetime) BETWEEN '2019-10-01' AND '2019-10-30'
GROUP BY DATE(lpep_pickup_datetime)
ORDER BY MAX(trip_distance) DESC;

--Question 5
WITH top_areas AS(
SELECT 
SUM(total_amount) AS total_fare,
"PULocationID"
FROM green_taxi_data
WHERE DATE(lpep_pickup_datetime) = '2019-10-18'
GROUP BY "PULocationID"
HAVING SUM(total_amount) > 13000)
SELECT *
FROM top_areas ta
JOIN taxi_zone tz
ON ta."PULocationID" = tz."LocationID"

--Question 6
SELECT
tp.total_tip,
tp."DOLocationID",
tp."PULocationID",
tz."LocationID",
tz.zone
FROM
(
SELECT
MAX(tip_amount) AS total_tip,
"DOLocationID",
"PULocationID"
FROM green_taxi_data
WHERE 
DATE_TRUNC('month', lpep_pickup_datetime) = '2019-10-01'
AND "PULocationID" = 74
GROUP BY "DOLocationID", "PULocationID"
ORDER BY MAX(tip_amount) DESC
LIMIT 1) tp
JOIN taxi_zone tz
ON tp."DOLocationID" = tz."LocationID";
