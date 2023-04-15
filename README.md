# Final_porject_SQL
ini adalah project pertama saya membuat relation table di postgre sql
bisa dilihat dibawah ini adalah table relation/ERD pada suatu table 
![image](https://user-images.githubusercontent.com/129209479/232206927-ea7db0f4-0752-4979-b726-c902db6b2874.png)

Ada 3 table yang saling berhubungan

ada beberapa query yang saya bisa kerjakan dibawah ini

Transactional Query

select * from cars;
output :
![image](https://user-images.githubusercontent.com/129209479/232210016-731f7163-831f-4098-83e1-e53b98112327.png)



SELECT cars.id, cars.brand, cars.model, cars.year, cars.price
FROM cars
WHERE cars.year >= 2015
output :
![image](https://user-images.githubusercontent.com/129209479/232209987-4a951b4e-57cb-405f-9934-55b5acfb1319.png)



SELECT * FROM cars WHERE brand = 'Honda' ORDER BY year DESC;
output :
![image](https://user-images.githubusercontent.com/129209479/232210061-b3c55543-ff4c-4cb7-9e4b-4a6e6333d64b.png)



SELECT brand, model, year, price, transmission FROM cars
WHERE brand LIKE '%Honda%'
AND (model LIKE '%Hatchback%' OR model LIKE '%Wagon%' OR model LIKE '%MPV%' OR model LIKE '%SUV%')
ORDER BY price ASC
LIMIT 1;
output :
![image](https://user-images.githubusercontent.com/129209479/232210093-08f706ae-fe15-42fd-b503-6a9546971fb2.png)



Analytical Query

SELECT 
  model, 
  COUNT(DISTINCT id) AS count_product, 
  COUNT(id) AS count_bid 
FROM cars 
GROUP BY model 
ORDER BY count_bid DESC;
output :
![image](https://user-images.githubusercontent.com/129209479/232210175-03a8e728-db8a-40c9-bbb1-dfe336fc8cda.png)


SELECT c.*, avg_car_city.avg_price AS avg_car_city
FROM cars c
JOIN (
    SELECT brand, AVG(price) AS avg_price
    FROM cars
    GROUP BY brand
) avg_car_brand ON c.brand = avg_car_brand.brand
JOIN (
    SELECT LEFT(transmission, 1) AS city_code, AVG(price) AS avg_price
    FROM cars
    GROUP BY LEFT(transmission, 1)
) avg_car_city ON LEFT(c.transmission, 1) = avg_car_city.city_code;

![image](https://user-images.githubusercontent.com/129209479/232210255-e7469efa-a02b-4d1d-adff-73e2b081164c.png)


SELECT 
    c.model AS "Model",
    AVG(c.price) AS "avg_price",
    AVG(b.bid) AS "avg_bid_6month",
    AVG(c.price) - AVG(b.bid) AS "difference",
    (AVG(c.price) - AVG(b.bid)) / AVG(c.price) * 100 AS "difference_percent"
FROM 
    cars c
    JOIN bids b ON c.id = b.car_id
WHERE 
    b.bid_date >= date_trunc('month', NOW() - INTERVAL '6 months') 
GROUP BY 
    c.model;
	output :
![image](https://user-images.githubusercontent.com/129209479/232210310-8ad6c79b-c348-408d-88c5-f566a50561d3.png)



SELECT brand, model,
       min(CASE WHEN year_month >= date_trunc('month', CURRENT_DATE - interval '6 month') THEN price END) AS m_min_6,
       min(CASE WHEN year_month = date_trunc('month', CURRENT_DATE - interval '5 month') THEN price END) AS m_min_5,
       min(CASE WHEN year_month = date_trunc('month', CURRENT_DATE - interval '4 month') THEN price END) AS m_min_4,
       min(CASE WHEN year_month = date_trunc('month', CURRENT_DATE - interval '3 month') THEN price END) AS m_min_3,
       min(CASE WHEN year_month = date_trunc('month', CURRENT_DATE - interval '2 month') THEN price END) AS m_min_2,
       min(CASE WHEN year_month = date_trunc('month', CURRENT_DATE - interval '1 month') THEN price END) AS m_min_1
FROM (
  SELECT brand, model, price, date_trunc('month', to_timestamp(cars.year::text, 'YYYY')::date + interval '1 month' * (cars.year - 1)::int) AS year_month
  FROM cars
) subquery
WHERE year_month >= date_trunc('month', CURRENT_DATE - interval '6 month')
GROUP BY brand, model;
output :
![image](https://user-images.githubusercontent.com/129209479/232210360-783f1035-6a97-4b11-a324-9a5c5a3eb96a.png)
mohon maaf ada kesalahan

