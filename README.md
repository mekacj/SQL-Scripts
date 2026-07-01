# SQL-Scripts
Data cleaning for the COVID-19 dataset from January to August 2020

-- Check how many rows were imported
SELECT COUNT(*) FROM covid_data;

-- Preview the data
SELECT * FROM covid_data LIMIT 10;

-- Check for NULLs
SELECT 
    SUM(CASE WHEN province IS NULL THEN 1 ELSE 0 END) AS null_province,
    SUM(CASE WHEN recovered IS NULL THEN 1 ELSE 0 END) AS null_recovered
FROM covid_data;

UPDATE covid_data
SET province = 'N/A'
WHERE province IS NULL;

UPDATE covid_data
SET recovered = 0
WHERE recovered IS NULL;

-- First check how it was imported
DESCRIBE covid_data;

-- If report_date came in as TEXT, convert it:
ALTER TABLE covid_data ADD COLUMN clean_date DATE;
UPDATE covid_data SET clean_date = STR_TO_DATE(report_date, '%Y-%m-%d');
ALTER TABLE covid_data DROP COLUMN report_date;
ALTER TABLE covid_data RENAME COLUMN clean_date TO report_date;

-- Confirm no more NULLs
SELECT 
    SUM(CASE WHEN province IS NULL THEN 1 ELSE 0 END) AS null_province,
    SUM(CASE WHEN recovered IS NULL THEN 1 ELSE 0 END) AS null_recovered
FROM covid_data;

-- Quick summary by country
SELECT country, SUM(confirmed) AS total_confirmed, 
       SUM(deaths) AS total_deaths
FROM covid_data
GROUP BY country
ORDER BY total_confirmed DESC
LIMIT 50;
