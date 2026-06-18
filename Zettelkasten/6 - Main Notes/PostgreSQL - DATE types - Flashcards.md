
Theory for the cards: [[PostgreSQL - DATE types]]

FILE TAGS: sql

Q: Напишите запрос, который выведет имена пилотов, которые в качестве второго пилота (second_pilot_id) в августе 2023 года летали в New York
![[Pasted image 20260325175715.png]]
A:    
```sql
SELECT p.name
FROM Pilots p  
JOIN Flights f ON f.second_pilot_id = p.pilot_id
WHERE EXTRACT(YEAR FROM f.flight_date) = '2023' 
    AND EXTRACT(MONTH FROM f.flight_date) = 8
    AND f.destination = 'New York';
```
<!--ID: 1774450655544-->
