# Netflix Movies and TV shows Data Analysis using SQL

![Netflix logo](https://github.com/abhikk1509/netflix_sql_project/blob/main/Netflix%20logo.png)

## Overview
This project aims to perform a detailed analysis of Netflix's movies and TV shows data using SQL. The objective is to derive actionable insights and answer key business questions that can inform decision-making. By leveraging the dataset, patterns in content distribution, viewership trends, and other factors influencing user engagement were explored. The findings provide valuable information for stakeholders seeking to optimize content strategy and enhance user experience.

## Objectives
1. Analyse the distribution of content types (movies vs TV shows).
2. Identify the most popular movies and TV shows.
3. List and analyse content based on release years, countries, and duration.
4. List percentage of content added in a specific country in each year
5. Explore and categorize content based on specific criteria and keywords.

## Dataset
The data for this project is sourced from the Kaggle dataset:

**Dataset Link:** (https://www.kaggle.com/datasets/shivamb/netflix-shows)

## Schema

```sql
CREATE TABLE Netflix
(show_id VARCHAR(50),
type VARCHAR(50),
title VARCHAR(150),	
director VARCHAR(250),
casts VARCHAR(1000),	
country	VARCHAR(150),
date_added	VARCHAR(50),
release_year INT,
rating VARCHAR(50),	
duration VARCHAR(50),	
listed_in VARCHAR(50),	
description VARCHAR(350)
)
```  
## Business problems and their solutions
## 1. Count the number of Movies vs TV Shows
```sql
SELECT 
type,
COUNT(*) AS type_of_content
FROM Netflix
GROUP BY type
```

## 2. Find the most common rating for movies and TV shows
```sql
SELECT
type, 
rating
FROM
(SELECT
type,
rating,
COUNT(*) AS most_common_rating,
RANK() OVER 
(PARTITION BY type 
ORDER BY COUNT(*) DESC) AS top_rating
FROM Netflix
GROUP BY 1,2) AS sub_query -- alias for code clarity
WHERE top_rating = 1
```

## 3. List all movies released in a specific year (e.g., 2020)

```sql
SELECT 
*
FROM Netflix
WHERE type = 'Movie'
AND release_year = 2020
```

## 4. Find the top 5 countries with the most content on Netflix

```sql
SELECT
TRIM(UNNEST(STRING_TO_ARRAY(country, ','))) AS upd_country,
COUNT(show_id) AS total_content
FROM
netflix
GROUP BY 1
ORDER BY 2 DESC
LIMIT 5
```

## 5. Identify the longest movie

```sql
SELECT
type,
title,
SPLIT_PART (duration, ' ', 1) :: INT 
FROM
netflix
WHERE type IN ('Movie') AND duration IS NOT NULL
ORDER BY 3 DESC
LIMIT 1
```

## 6. Find content added in the last 5 years

```sql
SELECT *
FROM netflix
WHERE date_added:: DATE >= current_date - INTERVAL '5 years'
```

## 7. Find all the movies/TV shows by director 'Rajiv Chilaka'!

```sql
SELECT *
FROM netflix
WHERE director ILIKE '%Rajiv Chilaka%'
```


## 8. List all TV shows with more than 5 seasons
```sql
SELECT 
*
FROM netflix
WHERE type = 'TV Show'
AND SPLIT_PART(duration, ' ', 1) :: INT > 5
```

## 9. Count the number of content items in each genre

```sql
SELECT
TRIM(UNNEST(STRING_TO_ARRAY(listed_in, ','))) AS genre,
COUNT (show_id) AS number_of_content
FROM
netflix
GROUP BY 1
```

## 10.Find each year and the average numbers of content release in India on netflix

```sql
WITH YearlyContent AS (
    SELECT
        EXTRACT(YEAR FROM date_added::DATE) AS release_year,
        COUNT(show_id) AS total_content
    FROM netflix
    WHERE country = 'India'
    GROUP BY 1
)
SELECT
    release_year,
    total_content,
    ROUND(AVG(total_content) OVER (), 0) AS avg_content_per_year
FROM YearlyContent
ORDER BY release_year;
```

## 11.Find content and percentage of content added in India in each year on netflix 

```sql
SELECT 
EXTRACT (YEAR FROM date_added:: DATE) AS release_year,
COUNT(Show_id) AS yearly_content,
ROUND(COUNT(Show_id)* 1.0 /(SELECT COUNT(*) FROM netflix WHERE country = 'India')*100 ,2)AS percentage_content_added_each_year
FROM netflix
WHERE country = 'India'
GROUP BY 1
ORDER BY 1
```

## 12. List all movies that are documentaries

```sql
SELECT 
*
FROM netflix
WHERE 'Documentaries' = ANY 
(SELECT 
TRIM(UNNEST(STRING_TO_ARRAY (listed_in, ','))))
AND type = 'Movie'
```

## 13. Find all content without a director

```sql
SELECT 
*
FROM netflix
WHERE director IS NULL
```

## 14. Return all movie records where actor 'Salman Khan' appeared and released in the last 10 years!

```sql
SELECT
*
FROM 
netflix
WHERE type = 'Movie'
AND casts ILIKE '%Salman Khan%'
AND release_year >= EXTRACT(YEAR FROM CURRENT_DATE) - 10
```

## 15. Find the top 10 actors who have appeared in the highest number of movies produced in India.

```sql
SELECT
top_10_actors
FROM
(SELECT
COUNT(show_id),
TRIM(UNNEST(STRING_TO_ARRAY(casts, ','))) AS top_10_actors
FROM netflix
WHERE type = 'Movie'
AND country ILIKE '%India%'
GROUP BY 2
ORDER BY 1 DESC
LIMIT 10) AS sub_query
```

## 16. Categorize the content based on the presence of the keywords 'kill' and 'violence' in the description field. Label content containing these keywords as 'Bad' and all other content as 'Good'. Count how many items fall into each category.

```sql
SELECT
category,
COUNT (*) AS total_content
FROM
(SELECT
*,
CASE 
WHEN description ILIKE '%kill%' OR description ILIKE '%violence%' THEN 'Bad'
ELSE 'Good'
END AS category
FROM 
netflix)
GROUP BY 1
```

## Findings
1.	Movies vs TV Shows: The query identifies the distribution of content between movies and TV shows, helping understand Netflix’s content strategy.
2.	Most Common Rating: The analysis reveals the most frequent rating for movies and TV shows, highlighting audience preferences.
3.	Top 5 Countries: Identifies the countries with the most content, indicating Netflix’s global reach and localized production.
4.	Longest Movie: Finds the longest movie, shedding light on content diversity and potential niche audience appeal.
5.	Content Categorization: Categorizes content as 'Bad' or 'Good' based on the presence of keywords like 'kill' and 'violence' in the description, helping Netflix analyse content tone.
   
This analysis offers valuable insights into Netflix's content, helping to shape content strategy and guide decision-making.

## Author – Abhik 
This is the first project in my portfolio, showcasing the SQL skills essential for data analysis. If you have any questions, feedback, or are interested in collaborating, feel free to reach out.

**LinkedIn:** (linkedin.com/in/abhik-kar-397b09130)

**email:** abhik666.ak@gmail.com



