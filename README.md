# Решение заданий из тренажера [SQL Academy](https://sql-academy.org/ru)



1. Вывести имена всех людей, которые есть в базе данных авиакомпаний [(сайт)](https://sql-academy.org/ru/trainer/tasks/1)

<details>
  <summary>Решение</summary>

```postgresql
SELECT name
FROM Passenger
```

</details>

2. Вывести названия всеx авиакомпаний [(сайт)](https://sql-academy.org/ru/trainer/tasks/2)

<details>
  <summary>Решение</summary>

```postgresql
SELECT name
FROM Company
```

</details>

3. Вывести все рейсы, совершенные из Москвы [(сайт)](https://sql-academy.org/ru/trainer/tasks/3)

<details>
  <summary>Решение</summary>

```postgresql
SELECT *
FROM Trip
WHERE town_from = 'Moscow'
```

</details>

4. Вывести имена людей, которые заканчиваются на "man" [(сайт)](https://sql-academy.org/ru/trainer/tasks/4)

<details>
  <summary>Решение</summary>

```postgresql
SELECT name
FROM Passenger
WHERE name LIKE '%man'
```

</details>

5. Вывести количество рейсов, совершенных на TU-134 [(сайт)](https://sql-academy.org/ru/trainer/tasks/5)

<details>
  <summary>Решение</summary>

```postgresql
SELECT COUNT(*) AS "count"
FROM Trip
WHERE plane = 'TU-154'
```

</details>

6. Какие компании совершали перелеты на Boeing [(сайт)](https://sql-academy.org/ru/trainer/tasks/6)

<details>
  <summary>Решение</summary>

```postgresql
SELECT DISTINCT Company.name
FROM Trip
	JOIN Company ON Trip.company = Company.id
WHERE Trip.plane = 'Boeing'
```

</details>

7. Вывести все названия самолётов, на которых можно улететь в Москву (Moscow)
   [(сайт)](https://sql-academy.org/ru/trainer/tasks/7)

<details>
  <summary>Решение</summary>

```postgresql
SELECT DISTINCT plane
FROM Trip
WHERE town_to = 'Moscow'
```

</details>

8. В какие города можно улететь из Парижа (Paris) и сколько времени это займёт?
   [(сайт)](https://sql-academy.org/ru/trainer/tasks/8)

<details>
  <summary>Решение</summary>

```postgresql
SELECT 
    town_to
    , (time_in - time_out) as flight_time
FROM Trip
WHERE town_from = 'Paris'
```

</details>

9. Какие компании организуют перелеты из Владивостока (Vladivostok)?
   [(сайт)](https://sql-academy.org/ru/trainer/tasks/9)

<details>
  <summary>Решение</summary>

```postgresql
SELECT c.name
FROM Trip t
JOIN Company c
    on t.company = c.id
WHERE town_from = 'Vladivostok'
```

</details>

10. Вывести вылеты, совершенные с 10 ч. по 14 ч. 1 января 1900 г. [(сайт)](https://sql-academy.org/ru/trainer/tasks/10)

<details>
  <summary>Решение</summary>

```postgresql
SELECT *
FROM Trip
WHERE time_out BETWEEN '1900-01-01 10:00:00' AND '1900-01-01 14:00:00'
```

</details>

11. Выведите пассажиров с самым длинным ФИО. Пробелы, дефисы и точки считаются частью имени.
    [(сайт)](https://sql-academy.org/ru/trainer/tasks/11)

<details>
  <summary>Решение</summary>

```postgresql
SELECT name
FROM Passenger
WHERE LENGTH(name) = (SELECT MAX(LENGTH(name)) FROM Passenger)
```

</details>

12. Выведите идентификаторы всех рейсов и количество пассажиров на них. Обратите внимание, что на каких-то рейсах пассажиров может не быть. В этом случае выведите число "0". [(сайт)](https://sql-academy.org/ru/trainer/tasks/12)

<details>
  <summary>Решение</summary>

```postgresql
SELECT 
    Trip.id
    , COALESCE(COUNT(Pass_in_trip.id), 0) as count
FROM Trip
LEFT JOIN Pass_in_trip
    ON Trip.id = Pass_in_trip.trip
GROUP BY Trip.id
```

</details>

13. Вывести имена людей, у которых есть полный тёзка среди пассажиров
    [(сайт)](https://sql-academy.org/ru/trainer/tasks/13)

<details>
  <summary>Решение</summary>

```postgresql
with cnt_names as (
    SELECT 
        Passenger.name
        , COUNT(*) as cnt
    FROM Passenger
    GROUP BY Passenger.name
)
SELECT name
from cnt_names
WHERE cnt > 1
```

</details>

14. В какие города летал Bruce Willis [(сайт)](https://sql-academy.org/ru/trainer/tasks/14)

<details>
  <summary>Решение</summary>

```postgresql
SELECT 
    DISTINCT town_to as town_to
FROM Trip
JOIN Pass_in_trip
    ON Trip.id = Pass_in_trip.trip
JOIN Passenger
    on Pass_in_trip.passenger = Passenger.id
WHERE Passenger.name = 'Bruce Willis'
```

</details>

15. Выведите идентификатор пассажира Стив Мартин (Steve Martin) и дату и время его прилёта в Лондон (London) 
[(сайт)](https://sql-academy.org/ru/trainer/tasks/15)

<details>
  <summary>Решение</summary>

```postgresql
SELECT 
    Pass_in_trip.passenger as id
    , Trip.time_in
FROM Trip
JOIN Pass_in_trip
    ON Trip.id = Pass_in_trip.trip
JOIN Passenger
    ON Pass_in_trip.passenger = Passenger.id
WHERE Passenger.name = 'Steve Martin' AND town_to = 'London'
```

</details>

16. Вывести идентификатор, имя и количество перелётов каждого пассажира, совершившего хотя бы 1 полёт. Результат отсортировать по количеству перелётов (по убыванию) и имени (по возрастанию). [(сайт)](https://sql-academy.org/ru/trainer/tasks/16)

<details>
  <summary>Решение</summary>

```postgresql
SELECT 
    Passenger.id
    , Passenger.name
    , COUNT(Pass_in_trip.Trip) as "count"
FROM Passenger
JOIN Pass_in_trip
    ON Passenger.id = Pass_in_trip.passenger
GROUP BY Passenger.id, Passenger.name
ORDER BY "count" DESC, Passenger.name
```

</details>

17. Определить, сколько потратил в 2005 году каждый из членов семьи. В результирующей выборке не выводите тех членов семьи, которые ничего не потратили. [(сайт)](https://sql-academy.org/ru/trainer/tasks/17)   

<details>
  <summary>Решение</summary>

```postgresql
SELECT 
    member_name
    , status
    , SUM(amount * unit_price) AS costs
FROM FamilyMembers
JOIN Payments
    ON member_id = family_member
WHERE TO_CHAR(date, 'YYYY') = '2005'
GROUP BY member_name, status
```

</details>

18. Выведите имя самого старшего человека. Если таких несколько, то выведите их всех. [(сайт)](https://sql-academy.org/ru/trainer/tasks/18) 

<details>
  <summary>Решение</summary>

```postgresql
SELECT member_name
FROM FamilyMembers
WHERE birthday = (SELECT MIN(birthday) FROM FamilyMembers)
```

</details>

19. Определить, кто из членов семьи покупал картошку (potato) [(сайт)](https://sql-academy.org/ru/trainer/tasks/19)

<details>
  <summary>Решение</summary>

```postgresql
SELECT DISTINCT status
FROM FamilyMembers
JOIN Payments
    ON member_id = family_member
JOIN Goods
    ON good = good_id
WHERE good_name = 'potato'
```

</details>

20. Сколько и кто из семьи потратил на развлечения (entertainment). Вывести статус в семье, имя, сумму 
[(сайт)](https://sql-academy.org/ru/trainer/tasks/20)

<details>
  <summary>Решение</summary>

```postgresql
SELECT 
    status
    , member_name
    , SUM(amount * unit_price) as costs
FROM FamilyMembers
JOIN Payments
    ON member_id = family_member
JOIN Goods
    ON good = good_id
JOIN GoodTypes
    ON type = good_type_id
WHERE good_type_name = 'entertainment'
GROUP BY status, member_name
```

</details>

21. Определить товары, которые покупали более 1 раза [(сайт)](https://sql-academy.org/ru/trainer/tasks/21)

<details>
  <summary>Решение</summary>

```postgresql
SELECT DISTINCT good_name
FROM Goods
JOIN Payments
    ON good_id = good
WHERE good_id in (
    SELECT 
        good
    FROM Payments
    GROUP BY good
    HAVING COUNT(*) > 1
)
```

</details>

22. Найти имена всех матерей (mother) [(сайт)](https://sql-academy.org/ru/trainer/tasks/22)

<details>
  <summary>Решение</summary>

```postgresql
SELECT member_name
FROM FamilyMembers
WHERE status = 'mother'
```

</details>

23. Найдите самый дорогой деликатес (delicacies) и выведите его цену  
[(сайт)](https://sql-academy.org/ru/trainer/tasks/23)

<details>
  <summary>Решение</summary>

```postgresql
SELECT 
    good_name
    , MAX(unit_price) as unit_price
FROM Goods
JOIN GoodTypes
    ON type = good_type_id
JOIN Payments
    ON good_id = good
WHERE good_type_name = 'delicacies'
GROUP BY good_name
ORDER BY unit_price DESC 
LIMIT 1
```

</details>

24. Определить, кто и сколько потратил в июне 2005 [(сайт)](https://sql-academy.org/ru/trainer/tasks/24)

<details>
  <summary>Решение</summary>

```postgresql
SELECT 
    member_name
    , SUM(amount * unit_price) as costs
FROM FamilyMembers
JOIN Payments
    ON member_id = family_member
WHERE TO_CHAR(date, 'YYYY-MM') = '2005-06'
GROUP BY member_name
```

</details>

25. Определить, какие товары не покупались в 2005 году [(сайт)](https://sql-academy.org/ru/trainer/tasks/25)

<details>
  <summary>Решение</summary>

```postgresql
WITH sum_table as (
    SELECT 
        good_id
        , SUM(amount) as qty
    FROM Goods
    LEFT JOIN Payments
        ON good_id = good
    WHERE TO_CHAR(date, 'YYYY') = '2005'
    GROUP BY good_id
)
SELECT good_name
FROM Goods
LEFT JOIN sum_table
    ON Goods.good_id = sum_table.good_id
WHERE qty is null
```

</details>

26. Определить группы товаров, которые не приобретались в 2005 году 
[(сайт)](https://sql-academy.org/ru/trainer/tasks/26)

<details>
  <summary>Решение</summary>

```postgresql
WITH cte AS (
    SELECT 
        good_type_id
        , SUM(amount) as sum_amount
    FROM GoodTypes
    JOIN Goods
        ON good_type_id = type
    JOIN Payments
        ON good_id = good
    WHERE EXTRACT(year FROM date) = 2005
    GROUP BY good_type_id
)
SELECT good_type_name
FROM GoodTypes
LEFT JOIN cte
    ON GoodTypes.good_type_id = cte.good_type_id
WHERE sum_amount is null
```

</details>

27. Узнайте, сколько было потрачено на каждую из групп товаров в 2005 году. Выведите название группы и потраченную на неё сумму. Если потраченная сумма равна нулю, т.е. товары из этой группы не покупались в 2005 году, то не выводите её. 
[(сайт)](https://sql-academy.org/ru/trainer/tasks/27)

<details>
  <summary>Решение</summary>

```postgresql
SELECT 
    good_type_name
    , SUM(amount * unit_price) as costs
FROM GoodTypes
JOIN Goods
    ON good_type_id = type
JOIN Payments
    ON good_id = good
WHERE EXTRACT(year FROM date) = 2005
GROUP BY good_type_name
```

</details>

28. Сколько рейсов совершили авиакомпании из Ростова (Rostov) в Москву (Moscow) ? 
[(сайт)](https://sql-academy.org/ru/trainer/tasks/28)  

<details>
  <summary>Решение</summary>

```postgresql
SELECT COUNT(*) as count
FROM Trip
WHERE town_from = 'Rostov' AND town_to = 'Moscow'
```

</details>

29. Выведите имена пассажиров, улетевших в Москву (Moscow) на самолете TU-134. В ответе не должно быть дубликатов. 
[(сайт)](https://sql-academy.org/ru/trainer/tasks/29)  

<details>
  <summary>Решение</summary>

```postgresql
SELECT DISTINCT "name"
FROM Passenger
JOIN Pass_in_trip
    ON Passenger.id = Pass_in_trip.passenger
JOIN Trip
    ON Pass_in_trip.trip = Trip.id
WHERE town_to = 'Moscow' AND plane = 'TU-134'
```

</details>

30. Вывести количество занятых мест по каждому рейсу из таблицы Pass_in_trip, отсортировав результат по убыванию количества занятых мест. [(сайт)](https://sql-academy.org/ru/trainer/tasks/30)  

<details>
  <summary>Решение</summary>

```postgresql
SELECT 
    trip
    , COUNT(*) as count
FROM Pass_in_trip
GROUP BY trip
ORDER BY COUNT(*) DESC 
```

</details>

31. Вывести всех членов семьи с фамилией Quincey. [(сайт)](https://sql-academy.org/ru/trainer/tasks/31) 

<details>
  <summary>Решение</summary>

```postgresql
SELECT *
FROM FamilyMembers
WHERE split_part(member_name, ' ', -1) = 'Quincey'
```

</details>

32. Вывести средний возраст людей (в годах), хранящихся в базе данных. Результат округлите до целого в меньшую сторону.
[(сайт)](https://sql-academy.org/ru/trainer/tasks/32) 

<details>
  <summary>Решение</summary>

```postgresql
SELECT FLOOR(AVG(date_part('year', age(CURRENT_DATE, birthday)))) as AGE
FROM FamilyMembers
```

</details>

33. Найдите среднюю цену икры на основе данных, хранящихся в таблице Payments. В базе данных хранятся данные о покупках красной (red caviar) и черной икры (black caviar). В ответе должна быть одна строка со средней ценой всей купленной когда-либо икры. [(сайт)](https://sql-academy.org/ru/trainer/tasks/33) 

<details>
  <summary>Решение</summary>

```postgresql
SELECT AVG(unit_price) as cost
FROM Payments
JOIN Goods
    ON good = good_id
WHERE good_name LIKE '%caviar%'
```

</details>

34. Сколько всего 10-ых классов [(сайт)](https://sql-academy.org/ru/trainer/tasks/34) 

<details>
  <summary>Решение</summary>

```postgresql
SELECT COUNT(*) as count
FROM Class
WHERE "name" LIKE '10%'
```

</details>

35. Сколько различных кабинетов школы использовались 2 сентября 2019 года для проведения занятий? 
[(сайт)](https://sql-academy.org/ru/trainer/tasks/35)

<details>
  <summary>Решение</summary>

```postgresql
SELECT COUNT(DISTINCT classroom) as count
FROM Schedule
WHERE date = '2019-09-02'
```

</details>

36. Выведите информацию об обучающихся, живущих на улице Пушкина (ul. Pushkina)? 
[(сайт)](https://sql-academy.org/ru/trainer/tasks/36)

<details>
  <summary>Решение</summary>

```postgresql
SELECT *
FROM Student
WHERE address LIKE '%Pushkin%'
```

</details>

37. Сколько лет самому молодому обучающемуся ? [(сайт)](https://sql-academy.org/ru/trainer/tasks/37)

<details>
  <summary>Решение</summary>

```postgresql
SELECT MIN(EXTRACT(year from AGE(CURRENT_DATE, birthday))) as year
FROM Student
```

</details>

38. Сколько учениц с именем Анна (Anna) учится в школе? [(сайт)](https://sql-academy.org/ru/trainer/tasks/38)

<details>
  <summary>Решение</summary>

```postgresql
SELECT COUNT(*) as count
FROM Student
WHERE first_name = 'Anna'
```

</details>

39. Сколько обучающихся в 10 B классе ? [(сайт)](https://sql-academy.org/ru/trainer/tasks/39)

<details>
  <summary>Решение</summary>

```postgresql
SELECT COUNT(*) as count
FROM Student_in_class
JOIN Class
    ON Student_in_class.class = Class.id
WHERE Class.name = '10 B'
```

</details>

40. Выведите название предметов, которые преподает Ромашкин П.П. (Romashkin P.P.). Обратите внимание, что в базе данных есть несколько учителей с такой фамилией. 
[(сайт)](https://sql-academy.org/ru/trainer/tasks/40)

<details>
  <summary>Решение</summary>

```postgresql
SELECT DISTINCT Subject.name as subjects
FROM Subject
JOIN Schedule
    ON Subject.id = Schedule.subject
JOIN Teacher
    ON Schedule.teacher = Teacher.id
WHERE Teacher.last_name || ' ' || LEFT(Teacher.first_name, 1) || '.' || LEFT (Teacher.middle_name, 1) || '.' = 'Romashkin P.P.'
```

</details>

41. Выясните, во сколько по расписанию начинается четвёртое занятие. [(сайт)](https://sql-academy.org/ru/trainer/tasks/41)

<details>
  <summary>Решение</summary>

```postgresql
SELECT start_pair
FROM Timepair
WHERE id = 4
```

</details>

42. Сколько времени обучающийся будет находиться в школе, учась со 2-го по 4-ый уч. предмет?
[(сайт)](https://sql-academy.org/ru/trainer/tasks/42)

<details>
  <summary>Решение</summary>

```postgresql
SELECT end_pair - 
(SELECT MIN(start_pair) FROM Timepair WHERE id = 2) as time
FROM Timepair 
WHERE id = 4
```

</details>

43. Выведите фамилии преподавателей, которые ведут физическую культуру (Physical Culture). Отсортируйте преподавателей по фамилии в алфавитном порядке. [(сайт)](https://sql-academy.org/ru/trainer/tasks/43)

<details>
  <summary>Решение</summary>

```postgresql
SELECT Teacher.last_name
FROM Teacher
JOIN Schedule
    ON Teacher.id = Schedule.teacher
JOIN Subject
    ON Schedule.subject = Subject.id
WHERE Subject.name = 'Physical Culture'
GROUP BY Teacher.last_name
ORDER BY Teacher.last_name
```

</details>

44. Найдите максимальный возраст (количество лет) среди обучающихся 10 классов на сегодняшний день. Для получения текущих даты и времени используйте функцию NOW(). 
[(сайт)](https://sql-academy.org/ru/trainer/tasks/44)

<details>
  <summary>Решение</summary>

```postgresql
SELECT EXTRACT(year from AGE(NOW(), MIN(birthday))) as max_year
FROM Student
JOIN Student_in_class
    ON Student.id = Student_in_class.student
JOIN Class
    on Student_in_class.class = Class.id
WHERE Class.name LIKE '10%'
```

</details>

45. Какие кабинеты чаще всего использовались для проведения занятий? Выведите те, которые использовались максимальное количество раз. [(сайт)](https://sql-academy.org/ru/trainer/tasks/45)

<details>
  <summary>Решение</summary>

```postgresql
WITH cte as (
    SELECT 
        classroom
        , COUNT(classroom) as cnt
    FROM  Schedule
    GROUP BY classroom
)
SELECT 
    classroom
FROM cte
WHERE cnt = (SELECT MAX(cnt) from cte)
```

</details>

46. В каких классах ведёт занятия преподаватель "Krauze"? [(сайт)](https://sql-academy.org/ru/trainer/tasks/46)

<details>
  <summary>Решение</summary>

```postgresql
SELECT DISTINCT Class.name
FROM Class
JOIN Schedule
    ON Class.id = Schedule.class
JOIN Teacher
    ON Schedule.teacher = Teacher.id
WHERE Teacher.last_name = 'Krauze'
```

</details>

47. Сколько занятий провел Krauze 30 августа 2019 г.? [(сайт)](https://sql-academy.org/ru/trainer/tasks/47)

<details>
  <summary>Решение</summary>

```postgresql
SELECT COUNT(*) as count
FROM Teacher
JOIN Schedule
    ON Teacher.id = Schedule.teacher
WHERE Teacher.last_name = 'Krauze' and Schedule.date = '2019-08-30'
```

</details>

48. Выведите заполненность классов в порядке убывания [(сайт)](https://sql-academy.org/ru/trainer/tasks/48)

<details>
  <summary>Решение</summary>

```postgresql
SELECT 
    Class.name
    , COUNT(*) as count
FROM Class
JOIN Student_in_class
    ON Class.id = Student_in_class.class
GROUP BY Class.name
ORDER BY count DESC 
```

</details>

49. Какой процент обучающихся учится в "10 A" классе? Выведите ответ в диапазоне от 0 до 100 с округлением до четырёх знаков после запятой, например, 96.0201. [(сайт)](https://sql-academy.org/ru/trainer/tasks/49)

<details>
  <summary>Решение</summary>

```postgresql
WITH cte as (
    SELECT 
        Student_in_class.class
        , COUNT(*) as cnt
    FROM Student_in_class
    GROUP BY Student_in_class.class
)
SELECT 
    ROUND(
        cte.cnt * 1.0 / (SELECT SUM(cnt) FROM cte) * 100
        , 4
    ) as percent
FROM Class
JOIN cte
    ON Class.id = cte.class
WHERE Class.name = '10 A'
```

</details>

50. Какой процент обучающихся родился в 2000 году? Результат округлить до целого в меньшую сторону. 
[(сайт)](https://sql-academy.org/ru/trainer/tasks/50)

<details>
  <summary>Решение</summary>

```postgresql
with cte as (
    SELECT 
        EXTRACT(year FROM birthday) as "year"
        , COUNT(*) as cnt
    FROM Student
    GROUP BY "year"
)
SELECT 
    FLOOR(cnt * 1.0 / (SELECT SUM(cnt) FROM cte) * 100) as percent
FROM cte
WHERE "year" = 2000
```

</details>

51. Добавьте товар с именем "Cheese" и типом "food" в список товаров (Goods). 
[(сайт)](https://sql-academy.org/ru/trainer/tasks/51)

<details>
  <summary>Решение</summary>

```postgresql
INSERT INTO Goods
SELECT 
    COALESCE(MAX(good_id) + 1, 1)
    , 'Cheese'
    , (SELECT good_type_id FROM GoodTypes WHERE good_type_name = 'food')
FROM Goods
```

</details>

52. Добавьте в список типов товаров (GoodTypes) новый тип "auto". [(сайт)](https://sql-academy.org/ru/trainer/tasks/52)

<details>
  <summary>Решение</summary>

```postgresql
INSERT INTO GoodTypes
SELECT 
    COALESCE(MAX(good_type_id) + 1, 1)
    , 'auto'
FROM GoodTypes
```

</details>

53. Измените имя "Andie Quincey" на новое "Andie Anthony". [(сайт)](https://sql-academy.org/ru/trainer/tasks/53)

<details>
  <summary>Решение</summary>

```postgresql
UPDATE FamilyMembers
SET member_name = 'Andie Anthony'
WHERE member_name = 'Andie Quincey'
```

</details>

54. Удалить всех членов семьи с фамилией "Quincey". [(сайт)](https://sql-academy.org/ru/trainer/tasks/54)

<details>
  <summary>Решение</summary>

```postgresql
DELETE FROM FamilyMembers
WHERE member_name LIKE '% Quincey'
```

</details>

55. Удалить компании, совершившие наименьшее количество рейсов. [(сайт)](https://sql-academy.org/ru/trainer/tasks/55)

<details>
  <summary>Решение</summary>

```postgresql
WITH cte as (
    SELECT 
        company
        , COUNT(*) as cnt
    FROM Trip
    GROUP BY company
),
min_companies as (
    SELECT Company.id
    FROM Company
    JOIN cte
        ON Company.id = cte.company
    WHERE cte.cnt = (SELECT MIN(cnt) FROM cte)
)
DELETE FROM Company
WHERE Company.id in (SELECT id FROM min_companies)
```

</details>

56. Удалить все перелеты, совершенные из Москвы (Moscow). [(сайт)](https://sql-academy.org/ru/trainer/tasks/56)

<details>
  <summary>Решение</summary>

```postgresql
DELETE  FROM Trip
WHERE town_from = 'Moscow'
```

</details>

57. Перенести расписание всех занятий на 30 мин. вперед. [(сайт)](https://sql-academy.org/ru/trainer/tasks/57)

<details>
  <summary>Решение</summary>

```postgresql
UPDATE Timepair
set
    start_pair = start_pair + interval '30 minutes'
    , end_pair = end_pair + interval '30 minutes'
```

</details>

58. Добавить отзыв с рейтингом 5 на жилье, находящиеся по адресу "11218, Friel Place, New York", от имени "George Clooney" [(сайт)](https://sql-academy.org/ru/trainer/tasks/58)

<details>
  <summary>Решение</summary>

```postgresql
WITH cte as (
    SELECT 
        Reservations.id
    FROM Users
    LEFT JOIN Reservations
        ON Users.id = Reservations.user_id
    LEFT JOIN Rooms
        ON Reservations.room_id = Rooms.id
    LEFT JOIN Reviews
        ON Reservations.id = Reviews.reservation_id
    WHERE Users.name = 'George Clooney' AND Rooms.address LIKE '%Friel Place%'
)
INSERT INTO Reviews
SELECT 
    COALESCE(MAX(id) + 1, 1)
    , (SELECT id FROM cte)
    , 5
FROM Reviews
```

</details>

59. Вывести пользователей,указавших Белорусский номер телефона ? Телефонный код Белоруссии +375. 
[(сайт)](https://sql-academy.org/ru/trainer/tasks/59)

<details>
  <summary>Решение</summary>

```postgresql
SELECT *
FROM Users
WHERE phone_number LIKE '+375%'
```

</details>

60. Выведите идентификаторы преподавателей, которые хотя бы один раз за всё время преподавали в каждом из одиннадцатых классов. [(сайт)](https://sql-academy.org/ru/trainer/tasks/60)

<details>
  <summary>Решение</summary>

```postgresql
SELECT teacher
FROM Class cl
JOIN Schedule sc ON sc.class = cl.id
WHERE cl.name LIKE '11%'
GROUP BY teacher
HAVING COUNT(DISTINCT cl.name) = (
    SELECT COUNT(*) 
    FROM Class 
    WHERE name LIKE '11%'
)
```

</details>

61. Выведите список комнат, которые были зарезервированы хотя бы на одни сутки в 12-ую неделю 2020 года. В данной задаче в качестве одной недели примите период из семи дней, первый из которых начинается 1 января 2020 года. Например, первая неделя года — 1–7 января, а третья — 15–21 января. [(сайт)](https://sql-academy.org/ru/trainer/tasks/61)

<details>
  <summary>Решение</summary>

```postgresql
SELECT DISTINCT Rooms.*
FROM Rooms
JOIN Reservations r ON Rooms.id = r.room_id
WHERE 
    EXTRACT(doy FROM r.start_date) <= 84
    AND EXTRACT(doy FROM r.end_date) >= 78
    AND EXTRACT(YEAR FROM r.start_date) = 2020
```

</details>

62. Вывести в порядке убывания популярности доменные имена 2-го уровня, используемые пользователями для электронной почты. Полученный результат необходимо дополнительно отсортировать по возрастанию названий доменных имён. 
[(сайт)](https://sql-academy.org/ru/trainer/tasks/62)

<details>
  <summary>Решение</summary>

```postgresql
SELECT 
    right(email, length(email) - position('@' in email)) as domain
    , COUNT(
        right(email, length(email) - position('@' in email))
    ) as count
FROM Users
GROUP BY domain
ORDER BY count desc, right(email, length(email) - position('@' in email))
```

</details>

63. Выведите отсортированный список (по возрастанию) фамилий и имен студентов в виде Фамилия.И. 
[(сайт)](https://sql-academy.org/ru/trainer/tasks/63)

<details>
  <summary>Решение</summary>

```postgresql
SELECT 
    Student.last_name || '.' || LEFT(Student.first_name, 1) || '.' as name
FROM Student
ORDER BY Student.last_name, Student.first_name
```

</details>

64. Вывести количество бронирований по каждому месяцу каждого года, в которых было хотя бы 1 бронирование. Результат отсортируйте в порядке возрастания даты бронирования. [(сайт)](https://sql-academy.org/ru/trainer/tasks/64)

<details>
  <summary>Решение</summary>

```postgresql
SELECT 
    EXTRACT(year FROM start_date) as year
    , EXTRACT(month FROM start_date) as month
    , COUNT(*) as amount
FROM Reservations
GROUP BY year, month
ORDER BY year, month
```

</details>

65. Необходимо вывести рейтинг для комнат, которые хоть раз арендовали, как среднее значение рейтинга отзывов округленное до целого вниз. [(сайт)](https://sql-academy.org/ru/trainer/tasks/65)

<details>
  <summary>Решение</summary>

```postgresql
SELECT 
    room_id
    , FLOOR(AVG(rating)) as rating
FROM Reservations
    JOIN Reviews
        ON Reservations.id = Reviews.reservation_id
GROUP BY room_id
```

</details>

66. Вывести список комнат со всеми удобствами (наличие ТВ, интернета, кухни и кондиционера), а также общее количество дней и сумму за все дни аренды каждой из таких комнат. [(сайт)](https://sql-academy.org/ru/trainer/tasks/66)

<details>
  <summary>Решение</summary>

```postgresql
SELECT 
    home_type
    , address
    , COALESCE(SUM(total / Reservations.price), 0) as days
    , COALESCE(SUM(total), 0) as total_fee
FROM Rooms
    LEFT JOIN Reservations
        ON Rooms.id = Reservations.room_id
WHERE has_tv AND has_internet AND has_kitchen AND has_air_con
GROUP BY home_type, address
```

</details>

67. Вывести время отлета и время прилета для каждого перелета в формате "ЧЧ:ММ, ДД.ММ - ЧЧ:ММ, ДД.ММ", где часы и минуты с ведущим нулем, а день и месяц без. [(сайт)](https://sql-academy.org/ru/trainer/tasks/67)

<details>
  <summary>Решение</summary>

```postgresql
SELECT 
    TO_CHAR(time_out, 'HH24:MI, FMDD.FMMM') || ' - ' ||
        TO_CHAR(time_in, 'HH24:MI, FMDD.FMMM') as flight_time
FROM Trip
```

</details>

68. Для каждой комнаты, которую снимали как минимум 1 раз, найдите имя человека, снимавшего ее последний раз, и дату, когда он выехал [(сайт)](https://sql-academy.org/ru/trainer/tasks/68)

<details>
  <summary>Решение</summary>

```postgresql
WITH cte as (
    SELECT 
        Rooms.id as room_id
        , LAST_VALUE(Users.name) OVER (PARTITION BY Rooms.id ORDER BY end_date rows BETWEEN unbounded preceding AND unbounded following) as name
        , LAST_VALUE(end_date) OVER (PARTITION BY Rooms.id ORDER BY end_date) as end_date
    FROM Reservations
        JOIN Rooms
            ON room_id = Rooms.id
        JOIN Users
            ON user_id = Users.id
)
SELECT 
    room_id
    , MAX(name) as name
    , MAX(end_date) as end_date
FROM cte
GROUP BY room_id
```

</details>

69. Вывести идентификаторы всех владельцев комнат, что размещены на сервисе бронирования жилья и сумму, которую они заработали [(сайт)](https://sql-academy.org/ru/trainer/tasks/69)

<details>
  <summary>Решение</summary>

```postgresql
SELECT 
    owner_id
    , COALESCE(SUM(total), 0) as total_earn
FROM Rooms
    LEFT JOIN Reservations ON Rooms.id = Reservations.room_id
GROUP BY owner_id
```

</details>

70. Необходимо категоризовать жилье на economy, comfort, premium по цене соответственно <= 100, 100 < цена < 200, >= 200. В качестве результата вывести таблицу с названием категории и количеством жилья, попадающего в данную категорию [(сайт)](https://sql-academy.org/ru/trainer/tasks/70)

<details>
  <summary>Решение</summary>

```postgresql

```

</details>

71. Найдите какой процент пользователей, зарегистрированных на сервисе бронирования, хоть раз арендовали или сдавали в аренду жилье. Результат округлите до сотых. [(сайт)](https://sql-academy.org/ru/trainer/tasks/71)

<details>
  <summary>Решение</summary>

```postgresql

```

</details>

72. Выведите среднюю цену бронирования за сутки для каждой из комнат, которую бронировали хотя бы один раз. Среднюю цену необходимо округлить до целого значения вверх. [(сайт)](https://sql-academy.org/ru/trainer/tasks/72)

<details>
  <summary>Решение</summary>

```postgresql

```

</details>

73. Выведите id тех комнат, которые арендовали нечетное количество раз 
[(сайт)](https://sql-academy.org/ru/trainer/tasks/73)

<details>
  <summary>Решение</summary>

```postgresql

```

</details>

74. Выведите идентификатор и признак наличия интернета в помещении. Если интернет в сдаваемом жилье присутствует, то выведите «YES», иначе «NO». [(сайт)](https://sql-academy.org/ru/trainer/tasks/74)

<details>
  <summary>Решение</summary>

```postgresql

```

</details>

75. Выведите фамилию, имя и дату рождения студентов, кто был рожден в мае. 
[(сайт)](https://sql-academy.org/ru/trainer/tasks/75)

<details>
  <summary>Решение</summary>

```postgresql

```

</details>

76. Вывести имена всех пользователей сервиса бронирования жилья, а также два признака: является ли пользователь собственником какого-либо жилья (is_owner) и является ли пользователь арендатором (is_tenant). В случае наличия у пользователя признака необходимо вывести в соответствующее поле 1, иначе 0. 
[(сайт)](https://sql-academy.org/ru/trainer/tasks/76)

<details>
  <summary>Решение</summary>

```postgresql

```

</details>

77. Создайте представление с именем "People", которое будет содержать список имен (first_name) и фамилий (last_name) всех студентов (Student) и преподавателей(Teacher) [(сайт)](https://sql-academy.org/ru/trainer/tasks/77)

<details>
  <summary>Решение</summary>

```postgresql

```

</details>

78. Выведите всех пользователей с электронной почтой в «hotmail.com» 
[(сайт)](https://sql-academy.org/ru/trainer/tasks/78)

<details>
  <summary>Решение</summary>

```postgresql

```

</details>

79. Выведите поля id, home_type, price у всего жилья из таблицы Rooms. Если комната имеет телевизор и интернет одновременно, то в качестве цены в поле price выведите цену, применив скидку 10%.
[(сайт)](https://sql-academy.org/ru/trainer/tasks/79)

<details>
  <summary>Решение</summary>

```postgresql

```

</details>

80. Создайте представление «Verified_Users» с полями id, name и email, которое будет показывает только тех пользователей, у которых подтвержден адрес электронной почты. [(сайт)](https://sql-academy.org/ru/trainer/tasks/80)

<details>
  <summary>Решение</summary>

```postgresql

```

</details>
