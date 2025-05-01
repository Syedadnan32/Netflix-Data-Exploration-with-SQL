# Netflix-Data-Exploration-with-SQL
Data Analysis Project for Netflix 📺
This project displays a number of SQL queries run on a Netflix dataset in order to do data analysis and extract insights. By using practical data exploration tasks including filtering, aggregation, and trend analysis, the objective is to show off SQL expertise.

Key Features: Content Analysis by Type, Genre, and Year of Release on Netflix

Perspectives on the evolution of content trends

Checking the distribution of content by country

Finding the most often used actors, directors, and duplicate records

Use of complex SQL clauses such as GROUP BY, HAVING, and ORDER BY

🛠 Technologies & Tools Structured Query Language, or SQL

tested using Postgresql 

📁 Netflix datasets.sql: This file includes all of the SQL queries that were utilised for analysis.


DROP TABLE IF EXISTS NETFLIX;
CREATE TABLE NETFLIX (
	SHOW_ID VARCHAR(5),
	TYPE VARCHAR(10),
	TITLE VARCHAR(250),
	DIRECTOR VARCHAR(550),
	CASTS VARCHAR(1050),
	COUNTRY VARCHAR(550),
	DATE_ADDED VARCHAR(55),
	RELEASE_YEAR INT,
	RATING VARCHAR(15),
	DURATION VARCHAR(15),
	LISTED_IN VARCHAR(250),
	DESCRIPTION VARCHAR(550)
);

SELECT	* FROM NETFLIX

--1-- Count the Number of Movies vs TV Shows ?

SELECT
	TYPE,
	COUNT(*)
FROM
	NETFLIX
GROUP BY
	1;

--2-- count of movies released in each year ?

SELECT
	RELEASE_YEAR,
	COUNT(*) AS MOVIE_COUNT
FROM
	NETFLIX
WHERE
	TYPE = 'Movie'
GROUP BY
	RELEASE_YEAR
ORDER BY
	RELEASE_YEAR DESC;

--3-- average movie durations 

SELECT
	AVG(CAST(REPLACE(DURATION, ' min', '') AS INTEGER)) AS AVERAGE_MOVIE_DURATION
FROM
	NETFLIX
WHERE
	TYPE = 'Movie';

--4-- Find the Most Common Rating for Movies and TV Shows?

SELECT
	*
FROM
	NETFLIX
WITH
	RATINGCOUNTS AS (
		SELECT
			TYPE,
			RATING,
			COUNT(*) AS RATING_COUNT
		FROM
			NETFLIX
		GROUP BY
			TYPE,
			RATING
	),
	RANKEDRATINGS AS (
		SELECT
			TYPE,
			RATING,
			RATING_COUNT,
			RANK() OVER (
				PARTITION BY
					TYPE
				ORDER BY
					RATING_COUNT DESC
			) AS RANK
		FROM
			RATINGCOUNTS
	)
SELECT
	TYPE,
	RATING AS MOST_FREQUENT_RATING
FROM
	RANKEDRATINGS
WHERE
	RANK = 1;

--5-- List All Movies Released in a Specific Year (e.g., 2020)?	  
SELECT
	*
FROM
	NETFLIX
WHERE
	RELEASE_YEAR = 2020;

--6-- count of movies in each country 
SELECT
	COUNTRY,
	COUNT(*) AS TYPE_COUNT
FROM
	NETFLIX
WHERE
	COUNTRY IS NOT NULL
GROUP BY
	COUNTRY
ORDER BY
	TYPE_COUNT DESC
LIMIT
	10

--7--counting titles based on rating and type in descending order of frequency? 

SELECT
	*
FROM
	NETFLIX
SELECT
	RATING,
	TYPE,
	COUNT(*) AS RATING_COUNT
FROM
	NETFLIX
WHERE
	RATING IS NOT NULL
GROUP BY
	RATING,
	TYPE
ORDER BY
	RATING_COUNT DESC,
	RATING,
	TYPE

--8--Find Content Added in the Last 5 Years?

SELECT
	*
FROM
	NETFLIX
SELECT
	*
FROM
	NETFLIX
WHERE
	TO_DATE(DATE_ADDED, 'month dd,yyyy') >= CURRENT_DATE - INTERVAL '5years';

--9--Find All Movies/TV Shows by Director 'Rajiv Chilaka'?
SELECT
	*
FROM
	NETFLIX
SELECT
	DIRECTOR,
	TITLE,
	TYPE,
	RELEASE_YEAR,
	RATING,
	DURATION
FROM
	NETFLIX
WHERE
	DIRECTOR = 'Rajiv Chilaka';

--10-- List All TV Shows with More Than 5 Seasons ?
SELECT
	*
FROM
	NETFLIX
WITH
	TV_SHOW_SEASONS AS (
		SELECT
			*
		FROM
			NETFLIX
		WHERE
			TYPE = 'TV Show'
			AND DURATION LIKE '%Seasons'
			AND DURATION > '5 Seasons'
	)
SELECT
	*
FROM
	TV_SHOW_SEASONS;


select * from netflix

--11-- Movies & TVShows RELEASED MONTH IN NETFLIX ?

SELECT 
    TO_CHAR(TO_DATE(date_added, 'Month DD, YYYY'), 'Month') AS month_name,
    COUNT(*) AS total_count
FROM 
    netflix
WHERE 
    date_added IS NOT NULL
GROUP BY 
    month_name
ORDER BY 
    total_count DESC;


--12-- count of different movies  listed_in Netflix ?

SELECT
	LISTED_IN,
	COUNT(SHOW_ID) AS LISTED_IN_COUNT
FROM
	NETFLIX
GROUP BY
	LISTED_IN
ORDER BY
	LISTED_IN_COUNT DESC

--13-- count of movies listed_in Netflix  with year of listed_in ?

SELECT
	LISTED_IN,
	RELEASE_YEAR,
	COUNT(SHOW_ID) AS LISTED_IN_COUNT
FROM
	NETFLIX
GROUP BY
	RELEASE_YEAR,
	LISTED_IN
ORDER BY
	LISTED_IN_COUNT DESC

--14-- directors with their movies, tv shows with their released year ?

SELECT
	DIRECTOR,
	TYPE,
	RELEASE_YEAR,
	COUNT(SHOW_ID) AS TOTAL_COUNT
FROM
	NETFLIX
WHERE
	DIRECTOR IS NOT NULL
GROUP BY
	DIRECTOR,
	TYPE,
	RELEASE_YEAR
HAVING
	COUNT(SHOW_ID) >= 8
ORDER BY
	TOTAL_COUNT DESC
