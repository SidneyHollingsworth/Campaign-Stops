# Campaign-Stops

--Which locations did Clinton visit the most during the campaign?

SELECT 
	count(location) AS number_of_visits,
	location
FROM analysis.clinton_campaign_stops  
GROUP BY 2
ORDER BY 1 DESC

--How many events did Clinton do in October? How many distinct locations?

SELECT 
    count(*) AS total_events,
    count(distinct location) AS distinct_locations
FROM analysis.clinton_campaign_stops  
WHERE (date >= '20161001' AND date <= '20161031')

-----

UPDATE analysis.task_one_trump 
SET state = 'WA'
WHERE state = 'Washington';
UPDATE analysis.task_one_trump 
SET state = 'DC'
WHERE location LIKE 'Washington DC,%';
UPDATE analysis.task_one_trump 
SET location = 'Prescott Valley, AZ',
	state = 'AZ',
	city = 'Prescott Valley'
  
-----

SELECT 
    state,
	count(*) AS COUNT
FROM analysis.task_one_trump
WHERE 
    date >= ((SELECT max(date) FROM analysis.task_one_trump) - 30) --assuming that trump's last event was on election day
GROUP BY 
    state
ORDER BY 2 DESC


-------


SELECT 
    state,
    CASE 
        WHEN state in ('CA', 'IL', 'NY', 'CO') THEN 'blue state'
        WHEN state in ('PA', 'NH', 'NV', 'OH', 'MI', 'IA', 'FL', 'NC', 'AZ') THEN 'swing state'
        ELSE 'red state'
    END AS partisan_status,
    count(*) AS clinton_visits
FROM analysis.clinton_campaign_stops
GROUP by 1, 2
ORDER BY 3 DESC


----

