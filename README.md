# DEFORESTATION AMONG COUNTRIES AND THEIR INCOME_GROUPS (1990-2016) - AN SQL PROJECT.
#### DATA ASSESSMENT AND CLEANING.

#### EFFECTING CHANGES TO COUNTRY NAME FROM 'BAHAMAS, THE' TO 'THE BAHAMAS' AND'GAMBIA, THE' TO 'THE GAMBIA'
```
UPDATE FOREST_AREA SET COUNTRY_NAME = 
CASE 
	WHEN COUNTRY_NAME = 'BAHAMAS, THE' THEN 'The Bahamas'
	WHEN COUNTRY_NAME = 'GAMBIA, THE' THEN 'The Gambia'
else country_name
END;
```
#### CHANGING OF COUNTRY_NAME FROM 'CABO VERDE' TO 'Cape Verde'
```
UPDATE FOREST_AREA 
SET COUNTRY_NAME = 'Cape Verde'
WHERE COUNTRY_NAME =
(SELECT DISTINCT COUNTRY_NAME FROM FOREST_AREA
WHERE COUNTRY_NAME = 'CABO VERDE'
);
```
#### COUNTRIES WITH  NULL VALUES IN THE FOREST_AREA_SQKM COLUMN FOR THE ENTIRE SCOPE(1989-2016)  USING SUBQUERY 
```
SELECT COUNTRY_NAME FROM 
(SELECT DISTINCT COUNTRY_NAME,YEAR, 
DENSE_RANK() OVER(PARTITION BY COUNTRY_NAME ORDER BY YEAR DESC ) Y FROM Forest_Area
WHERE forest_area_sqkm IS NULL AND YEAR >= 1989  ) W 
WHERE Y = 27
;
```
#### DELETED COUNTRIES WITH  NULL VALUES IN THE FOREST_AREA_SQKM COLUMN FOR THE ENTIRE SCOPE(1989-2016)  USING SUBQUERY 
```
DELETE FROM Forest_Area WHERE country_name IN 
(SELECT COUNTRY_NAME FROM 
(SELECT DISTINCT COUNTRY_NAME,YEAR, 
DENSE_RANK() OVER(PARTITION BY COUNTRY_NAME ORDER BY YEAR DESC ) Y FROM Forest_Area
WHERE forest_area_sqkm IS NULL AND YEAR >= 1989  ) W 
WHERE Y = 27)
;
```
#### DELETED COUNTRIES WHERE 21 OUT OF THE 27 forest_area_sqkm IS NULL
 ```
DELETE FROM Forest_Area WHERE forest_area_sqkm IS NULL AND country_name  IN 
 (SELECT COUNTRY_NAME FROM
 (SELECT DISTINCT country_name,YEAR,
 DENSE_RANK() OVER(PARTITION BY COUNTRY_NAME ORDER BY YEAR DESC) U FROM FOREST_AREA
 WHERE forest_area_sqkm IS NULL) H 
 WHERE U =  21)
;
```
####  DELETED COUNTRIES WHERE 21 OUT OF THE 27 forest_area_sqkm IS NULL
 ```
DELETE FROM Forest_Area WHERE country_name  =
 (SELECT COUNTRY_NAME FROM
 (SELECT DISTINCT country_name,YEAR,
 DENSE_RANK() OVER(PARTITION BY COUNTRY_NAME ORDER BY YEAR DESC) U FROM FOREST_AREA
 WHERE forest_area_sqkm IS NULL) H 
 WHERE U =  21)
;
```
#### DELETED COUNTRIES WITH ONLY 6 OUT OF THE 27 forest_area_sqkm THAT IS NOT NULL i.e SUDAN 
```
DELETE FROM Forest_Area WHERE COUNTRY_NAME =
 (SELECT DISTINCT country_name FROM Forest_Area
  WHERE  country_name =  'SUDAN'  )
;
```
#### DELETED COUNTRIES WITH ONLY 6 OUT OF THE 27 forest_area_sqkm THAT IS NOT NULL i.e SOUTH SUDAN
```
DELETE FROM Forest_Area WHERE COUNTRY_NAME =
 (SELECT DISTINCT country_name FROM Forest_Area
  WHERE  country_name =  'SOUTH SUDAN'  )
;
```
#### UPDATED forest_area_sqkm FOR ETHIOPIA BETWEEN THE YEAR 1990-1992 WHERE forest_area_sqkm IS NULL WITH THE AVERAGE VALUE(MEAN) OF forest_area_sqkm 
```
UPDATE Forest_Area 
	SET forest_area_sqkm =(SELECT
	AVG(forest_area_sqkm)  FROM FOREST_AREA WHERE country_name = 'ETHIOPIA')
WHERE COUNTRY_NAME = 'ETHIOPIA' AND YEAR BETWEEN 1990 AND 1992
;
```
#### CONVERTED AND UPDATED THE INSERTED FLOAT AVERAGE VALUE TO INTEGER FOR ETHIOPIA BETWEEN YEAR 1990-1992 
```
UPDATE FOREST_AREA SET forest_area_sqkm =(
SELECT CAST(forest_area_sqkm AS INT) GROUPED_INT FROM FOREST_AREA
WHERE country_name = 'ETHIOPIA' AND YEAR BETWEEN 1990 AND 1992
GROUP BY CAST (forest_area_sqkm AS INT))
WHERE country_name = 'ETHIOPIA' AND YEAR BETWEEN 1990 AND 1992
;
```
#### USING THE MOST OCCURING forest_area_sqkm (MODE) TO FILL IN THE VALUE FOR St. Martin (French part) FOR YEAR 2016 
```
UPDATE Forest_Area 
SET forest_area_sqkm = 
(SELECT forest_area_sqkm FROM 
(SELECT DISTINCT(forest_area_sqkm),COUNT(forest_area_sqkm) Y 
FROM Forest_Area
WHERE COUNTRY_NAME = 'St. Martin (French part)'
GROUP BY forest_area_sqkm) AS A
WHERE Y = 26)
WHERE COUNTRY_NAME = 'St. Martin (French part)' AND YEAR = 2016
;
```
#### DELETED 'WORLD' FROM COUNTRY_NAME AS OUTLYERS FROM FOREST_AREA
```
DELETE FROM FOREST_AREA WHERE COUNTRY_NAME =
(SELECT DISTINCT COUNTRY_NAME FROM  FOREST_AREA 
WHERE COUNTRY_NAME = 'WORLD')
;
```
#### START OF LAND_AREA
```
SELECT * FROM LAND_AREA 
	WHERE COUNTRY_NAME ='SOUTH SUDAN' ;

SELECT DISTINCT COUNTRY_NAME FROM LAND_AREA 
	ORDER BY COUNTRY_NAME ASC;

SELECT DISTINCT country_name FROM LAND_AREA 
	WHERE TOTAL_AREA_SQ_MI IS NULL;
```
#### DELETED 'WORLD' FROM COUNTRY_NAME AS OUTLYERS FROM LAND_AREA
```
DELETE FROM LAND_AREA WHERE COUNTRY_NAME =
(SELECT DISTINCT COUNTRY_NAME FROM  LAND_AREA 
WHERE COUNTRY_NAME = 'WORLD')
;
```
#### USING PARTITION TO GET EACH COUNTRY AND THE YEAR WHERE TOTAL_AREA_SQ_MI IS NULL
```
SELECT DISTINCT(COUNTRY_NAME),YEAR,TOTAL_AREA_SQ_MI, 
DENSE_RANK() OVER(PARTITION BY COUNTRY_NAME ORDER BY YEAR DESC ) MODE_RANK FROM LAND_AREA
WHERE TOTAL_AREA_SQ_MI IS NULL;
```
#### USING SUBQUERY TO GET DISTINCT COUNTRY_NAME AND THEIR HIGHEST COUNT(MODE_RANK) OF NULL VALUE FOR TOTAL_AREA_SQ_MI
```
SELECT DISTINCT(COUNTRY_NAME),MAX(T) FROM
(SELECT DISTINCT(COUNTRY_NAME),YEAR,TOTAL_AREA_SQ_MI, 
DENSE_RANK() OVER(PARTITION BY COUNTRY_NAME ORDER BY YEAR DESC ) T FROM LAND_AREA
WHERE TOTAL_AREA_SQ_MI IS NULL) U
GROUP BY COUNTRY_NAME;
```
#### DELETED COUNTRIES WITH  NULL VALUES IN THE LAND_AREA_SQKM COLUMN FOR THE ENTIRE SCOPE(1989-2016) i.e SUDAN AND SOUTH SUDAN USING SUBQUERY 
```
 DELETE FROM LAND_AREA WHERE COUNTRY_NAME IN
( SELECT DISTINCT COUNTRY_NAME FROM
(SELECT DISTINCT(COUNTRY_NAME),YEAR,TOTAL_AREA_SQ_MI, 
DENSE_RANK() OVER(PARTITION BY COUNTRY_NAME ORDER BY YEAR DESC ) T FROM LAND_AREA
WHERE TOTAL_AREA_SQ_MI IS NULL) U
WHERE T = 27) 
;
```
#### CROSS CHECKING FOR LAND_AREA
```
 SELECT DISTINCT country_name FROM Land_Area
 WHERE  total_area_sq_mi IS  NULL;
```
#### UPDATED NULL VALUE IN total_area_sq_mi FOR BELGIUM  FOR YEAR BETWEEN 1990-1991
```
 UPDATE Land_Area 
 SET total_area_sq_mi = 
(SELECT total_area_sq_mi FROM 
(SELECT total_area_sq_mi,COUNT(total_area_sq_mi) AS MODE_VALUE FROM LAND_AREA
WHERE country_name = 'BELGIUM'
GROUP BY total_area_sq_mi
) 
 R WHERE MODE_VALUE =  17
)  WHERE COUNTRY_NAME = 'BELGIUM' AND YEAR  <= 1999 ;
```
#### EVALUATION OF LAND_AREA
```
 SELECT * FROM Land_Area
 WHERE total_area_sq_mi IS  NULL
  ORDER BY country_name;
  ```
#### EVALUATION OF LAND_AREA
```
 SELECT DISTINCT country_name,YEAR FROM Land_Area
 WHERE  total_area_sq_mi IS  NULL AND COUNTRY_NAME = 'Palau';
```
#### UPDATED NULL VALUE FOR Luxembourg IN total_area_sq_mi FOR YEAR BETWEEN 1990-1991
```
 UPDATE Land_Area 
 SET total_area_sq_mi = 
(SELECT total_area_sq_mi FROM 
(SELECT total_area_sq_mi,COUNT(total_area_sq_mi) AS MODE_VALUE FROM LAND_AREA
	WHERE country_name = 'Luxembourg'
		GROUP BY total_area_sq_mi
) 
 R WHERE MODE_VALUE =  17
)  WHERE COUNTRY_NAME = 'Luxembourg' AND YEAR  <= 1999 ;
```
#### UPDATED NULL VALUE FOR Marshall Islands IN total_area_sq_mi FOR YEAR 1990
```
 UPDATE Land_Area 
 SET total_area_sq_mi = 
(SELECT total_area_sq_mi FROM 
(SELECT total_area_sq_mi,COUNT(total_area_sq_mi) AS MODE_VALUE FROM LAND_AREA
WHERE country_name = 'Marshall Islands'
GROUP BY total_area_sq_mi
) 
 R WHERE MODE_VALUE =  26
)  WHERE COUNTRY_NAME = 'Marshall Islands' AND YEAR  = 1990 ;
```
#### UPDATED NULL VALUE FOR Micronesia, Fed. Sts. IN total_area_sq_mi FOR YEAR 1990
```
 UPDATE Land_Area 
 SET total_area_sq_mi = 
(SELECT total_area_sq_mi FROM 
(SELECT total_area_sq_mi,COUNT(total_area_sq_mi) AS MODE_VALUE FROM LAND_AREA
WHERE country_name = 'Micronesia, Fed. Sts.'
GROUP BY total_area_sq_mi
) 
 R WHERE MODE_VALUE =  26
)  WHERE COUNTRY_NAME = 'Micronesia, Fed. Sts.' AND YEAR  = 1990 ;
```
#### UPDATED NULL VALUE FOR Northern Mariana Islands IN total_area_sq_mi FOR YEAR 1990 USING MODE
```
 UPDATE Land_Area 
 SET total_area_sq_mi = 
(SELECT total_area_sq_mi FROM 
(SELECT total_area_sq_mi,COUNT(total_area_sq_mi) AS MODE_VALUE FROM LAND_AREA
WHERE country_name = 'Northern Mariana Islands'
GROUP BY total_area_sq_mi
) 
 R WHERE MODE_VALUE =  26
)  WHERE COUNTRY_NAME = 'Northern Mariana Islands' AND YEAR  = 1990 ;
```
#### UPDATED NULL VALUE FOR Palau IN total_area_sq_mi FOR YEAR IN 1990 USING MODE
```
 UPDATE Land_Area 
 SET total_area_sq_mi = 
(SELECT total_area_sq_mi FROM 
(SELECT total_area_sq_mi,COUNT(total_area_sq_mi) AS MODE_VALUE FROM LAND_AREA
WHERE country_name = 'Palau'
GROUP BY total_area_sq_mi
) 
 R WHERE MODE_VALUE =  26
)  WHERE COUNTRY_NAME = 'Palau' AND YEAR  = 1990 ;
```
#### CHANGING OF COUNTRY_NAME FROM 'BAHAMAS, THE' TO 'THE BAHAMAS'
```
UPDATE LAND_AREA 
SET COUNTRY_NAME = 'The Bahamas'
WHERE COUNTRY_NAME =
(SELECT DISTINCT COUNTRY_NAME FROM LAND_AREA
WHERE COUNTRY_NAME = 'BAHAMAS, THE'
);
```
#### CHANGING OF COUNTRY_NAME FROM 'GAMBIA, THE' TO 'THE GAMBIA'
```
UPDATE LAND_AREA 
SET COUNTRY_NAME = 'The Gambia'
WHERE COUNTRY_NAME =
(SELECT DISTINCT COUNTRY_NAME FROM LAND_AREA
WHERE COUNTRY_NAME = 'Gambia, The'
);
```
#### CHANGING OF COUNTRY_NAME FROM 'CABO VERDE' TO 'CAPE VERDE'
```
UPDATE LAND_AREA 
SET COUNTRY_NAME = 'CAPE VERDE'
WHERE COUNTRY_NAME =
(SELECT DISTINCT COUNTRY_NAME FROM LAND_AREA
WHERE COUNTRY_NAME = 'CABO VERDE'
);
```
#### EVALUATION FOR LAND_AREA
```
SELECT * FROM Land_Area
 WHERE total_area_sq_mi IS  NULL
  ORDER BY country_name;
```
#### START OF REGION
```
  SELECT * FROM REGION;
```
#### CHANGING OF COUNTRY_NAME FROM 'CABO VERDE' TO 'CAPE VERDE'
```
UPDATE REGION 
SET COUNTRY_NAME = 'Cape Verde'
WHERE COUNTRY_NAME =
(SELECT  COUNTRY_NAME FROM REGION
WHERE COUNTRY_NAME = 'CABO VERDE'
);
```
#### CHANGING OF COUNTRY_NAME FROM 'GAMBIA, THE' TO 'THE GAMBIA'
```
UPDATE REGION 
SET COUNTRY_NAME = 'The Gambia'
	WHERE COUNTRY_NAME =
(SELECT  COUNTRY_NAME FROM REGION
	WHERE COUNTRY_NAME = 'Gambia, The'
);
```
#### CHANGING OF COUNTRY_NAME FROM 'BAHAMAS, THE' TO 'THE BAHAMAS'
```
UPDATE REGION 
SET COUNTRY_NAME = 'The Bahamas'
WHERE COUNTRY_NAME =
(SELECT  COUNTRY_NAME FROM REGION
WHERE COUNTRY_NAME = 'BAHAMAS, THE'
);
```
#### DELETED 'WORLD' FROM COUNTRY_NAME AS OUTLYERS FROM REGION
```
DELETE FROM REGION WHERE COUNTRY_NAME =
(SELECT DISTINCT COUNTRY_NAME FROM REGION 
WHERE COUNTRY_NAME = 'WORLD')
;
```
#### PROBLEM STATEMENTS
```
1. WHAT ARE THE TOTAL NUMBER OF COUNTRIES INVOLVED IN DEFORETATION?
2. SHOW THE INCOME GROUPS OF COUNTRIES HAVING TOTAL AREA RANGING FROM
	75,000 TO 150,000 SQUARE METER
3.CALCULATE AVG. AREA IN SQUARE MILES FOR COUNTRIES IN THE 'UPPER MIDDLE INCOME REGION'
	COMPARE THE RESULT WITH THE REST OF THE CATEGORIES
4.DETERMINE THE TOTAL FOREST AREA IN SQUARE KM FOR COUNTRIES IN THE 'HIGH INCOME' GROUP
	COMPARE RESULT WITH THE REST OF THE INCOME CATEGORIES
5. SHOW COUNTRIES FROM EACH REGION (CONTINENT) HAVING THE HIGHEST TOTAL FOREST AREAS.
```





##### Find full project [here](https://github.com/Cleancent26/DATA_ANALYTICS/blob/main/DEFORESTATION_AMONG_COUNTRIES.sql)


