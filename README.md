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
FROM tmc.clinton 
ORDER BY date DESC 
LIMIT 5;`

<img width="255" alt="tmc_q1_query" src="https://user-images.githubusercontent.com/55712982/151003111-bc937a23-7b8b-4672-b78e-995a3562e02d.png">

<img width="238" alt="tmc_q1_result" src="https://user-images.githubusercontent.com/55712982/151003654-3710e7a2-1950-4586-9b5e-1796fbc87ef3.png">

Ans. New York City, NY; Philadelphia, PA; Raleigh, NC; Oakland, PA; Allendale, MI

#### Q2: Which locations did Clinton visit the most during the campaign?

`SELECT 
	count(location) AS number_of_visits,
	location
FROM analysis.clinton_campaign_stops  
GROUP BY 2
ORDER BY 1 DESC;`

<img width="398" alt="tmc_q2_query" src="https://user-images.githubusercontent.com/55712982/151004433-9997be9b-9292-4b21-8e26-b7ac1f599102.png">

<img width="298" alt="tmc_q2_result" src="https://user-images.githubusercontent.com/55712982/151004442-110d267e-53a3-4694-9475-0a0bda0cd2e1.png">

Ans. New York City, NY with 7 visits and Charlotte, NC with four visits

#### Q3: How many events did Clinton do in October? How many distinct locations?

`SELECT 
    count(*) AS total_events,
    count(distinct location) AS distinct_locations
FROM analysis.tmc_clinton  
WHERE (date >= '20161001' AND date <= '20161031');`

<img width="484" alt="Screen Shot 2022-01-25 at 9 04 29 AM" src="https://user-images.githubusercontent.com/55712982/151004748-211ebc3c-6b11-4158-b55a-2bf7f5eabae0.png">

<img width="288" alt="tmc_q3_result" src="https://user-images.githubusercontent.com/55712982/151004769-07ff11ed-31fc-4221-a062-185353e52845.png">

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

<img width="867" alt="Screen Shot 2022-01-25 at 9 05 16 AM" src="https://user-images.githubusercontent.com/55712982/151004980-f57e1c57-a277-4970-af04-5979ad19057c.png">

<img width="279" alt="tmc_q4_result" src="https://user-images.githubusercontent.com/55712982/151005000-981c70f1-5d33-485f-b95a-2971ff9e5b1b.png">

Ans. 53 visits to a swing state, 2 visits to a red state, and 12 visits to a blue state

#### Q5: What locations had the most combined visits between Trump and Clinton? (Use a join, if you can!)

`with cte_trump AS
(
	SELECT location, lat, lng, COUNT(1) total_count
	FROM tmc.trump
	GROUP BY location, lat, lng
	ORDER BY total_count DESC
),
cte_clinton AS
(
	SELECT location, lat, lng, COUNT(1) total_count
	FROM tmc.clinton
	GROUP BY location, lat, lng
	ORDER BY total_count DESC
)
SELECT t.location, c.total_count + t.total_count AS total_count
FROM cte_trump t 
JOIN cte_clinton c on t.lat = c.lat and t.lng = c.lng
ORDER BY total_count DESC;`

<img width="610" alt="Screen Shot 2022-01-25 at 1 53 24 PM" src="https://user-images.githubusercontent.com/55712982/151049522-39d18678-4374-4f5c-925d-5c576a9e610c.png">

<img width="287" alt="Screen Shot 2022-01-25 at 1 53 43 PM" src="https://user-images.githubusercontent.com/55712982/151049508-c14960c9-d6ff-44c5-9d49-e77945347575.png">

Ans. New York, NY with 11 visits.

#### Q6: CHALLENGE (not required): Use a window function to show the running count of events in a state by date.

`SELECT state, date,
       COUNT(state) OVER
         (PARTITION BY state ORDER BY date ASC) AS running_count
  FROM analysis.tmc_clinton;`

<img width="1017" alt="Screen Shot 2022-01-25 at 5 48 23 PM" src="https://user-images.githubusercontent.com/55712982/151078991-96e948c5-896e-4f56-9135-ac70a273e5fd.png">

<img width="456" alt="Screen Shot 2022-01-25 at 5 48 49 PM" src="https://user-images.githubusercontent.com/55712982/151078962-a7fff62a-6c84-4df3-b598-8f67f097fa7f.png">

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
