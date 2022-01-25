# Campaign-Stops
Completed Jan 25, 2022

---

## Task 1: SQL

### Resources Used
- Postgres
- pgAdmin
- QuickDatabaseDiagrams.com


#### Q1. What are the last five locations Clinton visited during the previous campaign?

`SELECT location, date
FROM analysis.clinton_campaign_stops  
ORDER BY date DESC
LIMIT 5;`

Ans. New York City, NY; Philadelphia, PA; Raleigh, NC; Oakland, PA; Allendale, MI

#### Q2: Which locations did Clinton visit the most during the campaign?

`SELECT 
	count(location) AS number_of_visits,
	location
FROM analysis.clinton_campaign_stops  
GROUP BY 2
ORDER BY 1 DESC;`

Ans. New York City, NY with 7 visits and Charlotte, NC with four visits

#### Q3: How many events did Clinton do in October? How many distinct locations?

`SELECT 
    count(*) AS total_events,
    count(distinct location) AS distinct_locations
FROM analysis.tmc_clinton  
WHERE (date >= '20161001' AND date <= '20161031');`

Ans. 32 total events and 29 distinct locations

#### Q4: How many states did Clinton visit: swing states vs. red states vs. blue states (define as you wish)?

`SELECT 
    CASE 
        WHEN state in ('CA', 'IL', 'NY', 'CO') THEN 'blue state'
        WHEN state in ('PA', 'NH', 'NV', 'OH', 'MI', 'IA', 'FL', 'NC', 'AZ') THEN 'swing state'
        ELSE 'red state'
    END AS partisan_status,
    count(*) AS clinton_visits
FROM analysis.tmc_clinton
GROUP by 1
ORDER BY 2 DESC;`

Ans. 53 visits to a swing state, 2 visits to a red state, and 12 visits to a blue state

#### Q5: What locations had the most combined visits between Trump and Clinton? (Use a join, if you can!)

`SELECT 
	count(location) AS number_of_visits,
	location
FROM analysis.tmc_clinton  
JOIN analysis.tmc_trump
ON clinton_campaign_stops.location = task_one_trump.location
GROUP BY 2
ORDER BY 1 DESC;`

Ans. 

#### Q6: CHALLENGE (not required): Use a window function to show the running count of events in a state by date.

https://mode.com/sql-tutorial/sql-window-functions/
https://alexchang7a.medium.com/sql-advanced-cheatsheet-window-functions-428113ef4a96
https://learnsql.com/blog/sql-window-functions-cheat-sheet/Window_Functions_Cheat_Sheet_Letter.pdf
https://www.postgresqltutorial.com/postgresql-window-function/
https://www.sqlservercentral.com/forums/topic/sql-query-running-count

Incorrect Draft:

`SELECT state, date,
       COUNT(state) OVER
         (PARTITION BY state ORDER BY date ASC) AS running_count
  FROM analysis.tmc_clinton;`


====


----- To Fix State Errors:

`UPDATE analysis.task_one_trump 
SET state = 'WA'
WHERE state = 'Washington';
UPDATE analysis.task_one_trump 
SET state = 'DC'
WHERE location LIKE 'Washington DC,%';
UPDATE analysis.task_one_trump 
SET location = 'Prescott Valley, AZ',
	state = 'AZ',
	city = 'Prescott Valley';`
  

----- Trump Count by State:

`SELECT 
    state,
	count(*) AS COUNT
FROM analysis.task_one_trump
WHERE 
    date >= ((SELECT max(date) FROM analysis.task_one_trump) - 30) --assuming that trump's last event was on election day
GROUP BY 
    state
ORDER BY 2 DESC`

---

## Task 2: Explanatory Email
- View [Sample Email]()
