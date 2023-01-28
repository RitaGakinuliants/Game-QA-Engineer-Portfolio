Выполнение задачи SQL Murder Mystery
***
# Практика в составлении SQL запросов
### Задание
С условиями задачи можно ознакиться здесь
https://mystery.knightlab.com/
Коротко: путем составления SQL запросов необходимо выяснить, кто совершил преступление.

### Выполнение

1. Нужно найти необходимые нам данные из доклада с места преступления. Знаем, что произошло убийство, 1 января 2018 года в SQL City.
  
  **SELECT * 
  FROM crime_scene_report
  WHERE type = 'murder' AND date = 20180115 AND city = 'SQL City'**

2. У нас есть два свидетеля.
Первый: живет в последнем доме на Northwestern Dr

**SELECT * 
FROM person
WHERE address_street_name = 'Northwestern Dr'
ORDER BY address_number DESC**

Данные первого свидетеля:

id|	name|	license_id|	address_number|	address_street_name|	ssn
|---|---|---|---|---|---|
14887|	Morty Schapiro|	118009|	4919|	Northwestern Dr	|111564949

Второй: ее зовут Annabel и она живет где-то на Franklin Ave

**SELECT * 
FROM person
WHERE name LIKE '%Annabel%' AND address_street_name = 'Franklin Ave'**

Данные второго свидетеля:

id|	name|	license_id|	address_number|	address_street_name	|ssn
|---|---|---|---|---|---|
16371|	Annabel Miller|	490173	|103	Franklin Ave	|318771143

3. У нас есть таблица interview... Кажется, самое время опросить свидетелей :)
 Используем FOREIGN KEY (person_id) - ну или можно посмотреть просто id в данных со свидетелями и сделать выборку по этому id в таблице с допросом.

**SELECT * 
FROM interview JOIN person ON interview.person_id = person.id
WHERE name = 'Morty Schapiro'**

Данные:
I heard a gunshot and then saw a man run out. He had a "Get Fit Now Gym" bag. The membership number on the bag started with "48Z". Only gold members have those bags. The man got into a car with a plate that included "H42W".

**SELECT * 
FROM interview JOIN person ON interview.person_id = person.id
WHERE name = 'Annabel Miller'**

Данные:
I saw the murder happen, and I recognized the killer from my gym when I was working out last week on January the 9th.

4. Пришло время собрать данные воедино. Убийца ходит в тренажерку Get Fit Now Gym, у него золотая карта, номер на его тренировочной сумке начинается с 48Z, номер на его машине содержит H42W и убийца точно был в тренажерном зале 9го января.
Узнаем, пользователи с какими клубными картами были в зале 9 января:

**SELECT *
FROM get_fit_now_check_in
WHERE check_in_date = 20180109 AND membership_id LIKE '48Z%'**

membership_id	|check_in_date	|check_in_time|	check_out_time
|---|---|---|---|
48Z7A	|20180109	|1600	|1730
48Z55|	20180109	|1530|	1700

Узнаем имена тех членов клуба, кто нам подходит: 

**SELECT *
FROM get_fit_now_member
WHERE membership_status = 'gold' AND id IN ('48Z7A', '48Z55')**

id	|person_id|	name|	membership_start_date|	membership_status
|---|---|---|---|---|
48Z55	|67318|	Jeremy Bowers|	20160101	|gold
48Z7A	|28819|	Joe Germuska|	20160305|	gold


И информацию о машинах, одно из которых было на месте преступления: 

**SELECT * 
FROM drivers_license 
WHERE plate_number LIKE '%H42W%'**

id	|age	|height	|eye_color|	hair_color|	gender|	plate_number	|car_make|	car_model
|---|---|---|---|---|---|---|---|---|
183779	|21|	65|	blue	|blonde|	female|	H42W0X|	Toyota|	Prius
423327	|30|	70|	brown	|brown	|male|	0H42W2	|Chevrolet	|Spark LS
664760	|21|	71|	black	|black|	male|	4H42WR|	Nissan|	Altima

Соединим данные по таблице машин и членов клуба, используя ключ id лицензий в таблице drivers_license

**SELECT * FROM 
(SELECT *
FROM person
WHERE id IN (67318, 28819)) AS a
JOIN 
(SELECT * 
FROM drivers_license 
WHERE plate_number LIKE '%H42W%') AS b
ON a.license_id	= b.id**

id	|name|	license_id	|address_number	|address_street_name|	ssn|	id|	age|	height|	eye_color|	hair_color|	gender|	plate_number|	car_make|	car_model
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
67318	|Jeremy Bowers|	423327	|530	|Washington Pl, Apt 3A	|871539279	|423327|	30|	70	|brown	|brown|	male|	0H42W2	|Chevrolet|	Spark LS

5. Итак, наш главный подозреваемый **Jeremy Bowers**
Послушаем, что он скажет?

**SELECT * 
FROM interview JOIN person ON interview.person_id = person.id
WHERE name = 'Jeremy Bowers'**

I was hired by a woman with a lot of money. I don't know her name but I know she's around 5'5" (65") or 5'7" (67"). She has red hair and she drives a Tesla Model S. I know that she attended the SQL Symphony Concert 3 times in December 2017.

Джереми говорит, что он был нанят и нам нужно найти, кто заказчик. (а я так надеялась, что Джереми и есть тот, кого мы искали...)

Чтож. Начнем еще раз.
Соберем все данные из таблицы с правами на машину со слов наемного убицы:

**SELECT * 
FROM drivers_license 
WHERE car_make = 'Tesla' AND car_model = 'Model S' 
                        AND gender = 'female' 
                        AND hair_color = 'red'**

id|	age|	height	|eye_color	|hair_color	|gender	|plate_number	|car_make	|car_model
|---|---|---|---|---|---|---|---|---|
202298|	68	|66	|green|	red	|female	|500123	|Tesla	|Model S
291182	|65	|66	|blue|	red|	female|	08CM64|	Tesla	|Model S
918773	|48	|65|	black|	red|	female	|917UU3	|Tesla	|Model S

По росту они все нам подходят.
Найдем их личные id 

**SELECT *
FROM person
WHERE license_id IN (202298, 291182, 918773)**

id	|name|	license_id	|address_number|	address_street_name	|ssn
|---|---|---|---|---|---|
78881	|Red Korb	|918773	|107	Camerata Dr	|961388910
90700|	Regina George	|291182	|332	Maple Ave|	337169072
99716	|Miranda Priestly	|202298	|1883	Golden Ave|	987756388


Попробуем поискать в фейсбуке, кто ходил на симфонический SQL концерт (что бы это ни значило :) в декабре 2017го аж 3 раза.

**SELECT * 
FROM facebook_event_checkin
WHERE person_id IN (78881, 90700, 99716) AND date LIKE '201712%%'**

person_id|	event_id|	event_name	|date
|---|---|---|---|
99716	|1143	|SQL Symphony Concert|	20171206
99716	|1143	|SQL Symphony Concert|	20171212
99716	|1143	|SQL Symphony Concert|	20171229

Все сходится. 
Итак, используя id выясняем, что заказчик - **Miranda Priestly**.

Пришло время проверить наши догадки!

Check your solution
Did you find the killer?
Insert the name of the person you found here

INSERT INTO solution VALUES (1, 'Miranda Priestly');
SELECT value FROM solution

**ОТВЕТ:**
**Congrats, you found the brains behind the murder! Everyone in SQL City hails you as the greatest SQL detective of all time. Time to break out the champagne!**

---

Потом я решила проверить, что будет, если вставить имя наемного убицы.
Congrats, you found the murderer! But wait, there's more... If you think you're up for a challenge, try querying the interview transcript of the murderer to find the real villain behind this crime. If you feel especially confident in your SQL skills, try to complete this final step with no more than 2 queries. Use this same INSERT statement with your new suspect to check your answer.

Предлагают использовать всего два запроса, а у меня плучилось 3.
Можно объединить первые два запроса.

**SELECT *
FROM person
WHERE license_id IN (SELECT id 
FROM drivers_license 
WHERE car_make = 'Tesla' AND car_model = 'Model S' 
                        AND gender = 'female' 
                        AND hair_color = 'red')**

А второй запрос можно подбить так, чтобы он давал нам то, что так нужно - имя.

**SELECT name
FROM person
WHERE id IN (SELECT person_id 
FROM facebook_event_checkin
WHERE person_id IN (78881, 90700, 99716) AND date LIKE '201712%%')**

name
Miranda Priestly
