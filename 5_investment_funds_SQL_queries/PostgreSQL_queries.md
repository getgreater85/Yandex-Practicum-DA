# Fetching data (Queries)

![2023-01-10_00-09-14](https://user-images.githubusercontent.com/114194866/211474878-3c01071d-310b-486f-88c4-a732c94deb3d.png)

In this project, we need to analyze the data about funds and investments and write appropriate SQL queries to the database.

### Summary contents <br>

[1.    Let's count how many companies have closed.](#T1) <br>
[2.    We will display the number of funds raised for U.S. news companies.](#T2) <br>
[3.    Let's find the total amount of transactions for the purchase of some companies by others in USD.](#T3) <br>
[4.    Let's display the first name, last name, and account names of people on Twitter whose account names begin with 'Silver'.
](#T4) <br>
[5.    Display all information about people whose twitter account names contain the substring 'money' and whose last name begins with 'K'](#T5)<br>
[6.    For each country, we will display the total amount of attracted investments that companies registered in this country received.](#T6)<br>
[7.    Let's make a table
which will include the date of the round, as well as the minimum and maximum values of the amount of investment raised on this date.](#T7)<br>
[8.    Let's create a field with categories. Display all fields of the `fund` table and a new field with categories.](#T8)<br>
[9.    For each of the categories assigned in the previous task,
Let's calculate the rounded average number of investment rounds in which the fund took part.](#T9)<br>
[10.    Let's analyze in which countries the funds that most often invest in startups are located.](#T10)<br>
[11.    We will display the first and last names of all startup employees.](#T11)<br>
[12.    For each company we find the number of educational institutions,
which its employees graduated from.](#T12)<br>
[13.    Let's make a list with the unique names of closed companies for which the first round of financing was the last.](#T13)<br>
[14.    Let's compile a list of unique numbers of employees who work in the companies selected in the previous task.](#T14)<br>
[15.    Let's make a table
which will include unique pairs with employee numbers from the previous task and the educational institution from which the employee graduated.](#T15)<br>
[16.    Let's calculate the number of educational institutions for each employee from the previous task.](#T16)<br>
[17.    Let's supplement the previous query and display the average number of educational institutions (all, not just unique ones),
which employees of different companies graduated from.](#T17)<br>
[18.    Let's write a similar query: we'll display the average number of educational institutions (all, not just unique ones) that Facebook employees graduated from.](#T18)<br>
[19.    Let's make a table from the fields: `name_of_fund` - name of the fund; `name_of_company` — company name; `amount` - investment amount,
which the company raised in the round. ](#T19)<br>
[20.    Let's upload a table that will contain the following fields: name of the purchasing company; transaction amount; the name of the company that was purchased; the amount of investment made in the acquired company; a share that reflects how many times the purchase amount exceeded the amount of investments made in the company,
rounded to the nearest whole number.](#T20)<br>
[21.    Let's download a table that will include the names of companies from the `social` category that received funding from 2010 to 2013 inclusive.](#T21)<br>
[22.    Let's select data for months from 2010 to 2013, when investment rounds took place.](#T22)<br>
[23.
   Let's create a summary table and display the average investment amount for countries that have startups registered in 2011, 2012 and 2013.](#T23)<br>

<br><a name="T1"></a>
#### 1. Let's count how many companies have closed.

In order to select data from a table, an SQL query with the following structure is used:


```SQL
SELECT COUNT(c.status)
FROM company c
WHERE c.status = 'closed';

```
Result: 
| count | 
|------   |  
|2584|
___
<a name="T2"></a>
#### 2. We will display the number of funds raised for US news companies. We use data from the `company` table. Let's sort the table in descending order of values ​​in the `funding_total` field.

```SQL
SELECT to_char(CAST(c.funding_total AS numeric), '9999999999FM') AS funding_total
FROM company c
WHERE c.category_code = 'news'
  AND country_code = 'USA'
ORDER BY c.funding_total DESC;

```
Result: 
| funding_total | 
|------   |  
|622553000||------   |  
|250000000||------   |  
|160500000||------   |  
|128000000||------   |  
|126500000||------   |  
|70000000||------   |  
|...|
___
<a name="T3"></a>
#### 3. Let's find the total amount of transactions for the purchase of some companies by others in dollars. We will select transactions that were carried out only in cash from 2011 to 2013 inclusive.

```SQL
SELECT SUM(a.price_amount)
FROM acquisition a
WHERE a.term_code = 'cash'
  AND EXTRACT(YEAR
              FROM acquired_at::date) IN (2011,
                                          2012,
                                          2013);

```
Result: 
| sum | 
|------   |  
|1.37762e+11|
___
<a name="T4"></a>
#### 4. Отобразим имя, фамилию и названия аккаунтов людей в твиттере, у которых названия аккаунтов начинаются на 'Silver'.

```SQL
SELECT p.first_name,
       p.last_name,
       p.twitter_username
FROM people p
WHERE p.twitter_username LIKE 'Silver%';

```
Result: 
| first_name | last_name | twitter_username |
|------   |  ------- |  ------|
|Rebecca	| Silver| SilverRebecca|
|Silver		|Teede| SilverMatrixx|
|Mattias	|Guilotte| Silverreven|
___
<a name="T5"></a>
#### 5. Выведем на экран всю информацию о людях, у которых названия аккаунтов в твиттере содержат подстроку 'money', а фамилия начинается на 'K'.

```SQL
SELECT *
FROM people p
WHERE p.twitter_username LIKE '%money%'
  AND p.last_name LIKE 'K%';

```
Result: 
| id			| first_name | last_name | company_id | twitter_username | created_at | updated_at |
|------   |  ------- |  ------|  ------|  ------|  ------|  ------|
|63081	  | Gregory	| Kim | | gmoney75 | 2010-07-13 03:46:28| 2011-12-12 22:01:34|
___
<a name="T6"></a>
#### 6. Для каждой страны отобразим общую сумму привлечённых инвестиций, которые получили компании, зарегистрированные в этой стране. Страну, в которой зарегистрирована компания, можно определить по коду страны. Отсортируем данные по убыванию суммы.
```SQL
SELECT c.country_code,
       SUM(c.funding_total)
FROM company c
GROUP BY c.country_code
ORDER BY SUM(c.funding_total) DESC;

```
Result: 
| country_code | sum |
|------   |  ------- |
|USA	  | 31058800000	|
|GBR	  | 1770560000	|
|   	  | 1085590000	|
|CHN	  | 1068970000	|
|CAN	  | 1058470000	|
|IND	  | 1049970000	|
|DEU	  | 1038100000	|
|FRA	  | 614141000	|
|ISR	  | 514199000	|
|CHE	  | 424152300	|
|NLD	  | 22415330	|

___
<a name="T7"></a>
#### 7. Составим таблицу, в которую войдёт дата проведения раунда, а также минимальное и максимальное значения суммы инвестиций, привлечённых в эту дату.
Оставим в итоговой таблице только те записи, в которых минимальное значение суммы инвестиций не равно нулю и не равно максимальному значению.

```SQL
SELECT fr.funded_at,
       MIN(fr.raised_amount),
       MAX(fr.raised_amount)
FROM funding_round fr
GROUP BY fr.funded_at
HAVING MIN(fr.raised_amount) <> 0
AND MIN(fr.raised_amount) <> MAX(fr.raised_amount);

```
Result: 
| funded_at |	min |	max | 
|------   |------   |------   |  
| 2012-08-22 |	40000 |	7.5e+07 | 
| 2010-07-25 |	3.27825e+06| 	9e+06 |
| 2002-03-01 |	2.84418e+06 |	8.95915e+06 | 
| 2010-10-11 |	28000 |	2e+08 | 
| 2007-01-18 |	5.5e+06 |	2.3e+07 | 
| 2007-02-27 |	1.29e+06 |	3.6e+07 | 
| 2006-01-05 |	8.9e+06 |	2.65e+07 | 
| 2011-10-31 |	35000 |	2.5e+07 | 
| 2012-10-27 |	500000 |	9.3e+06 | 
| 2007-08-16 |	2.51989e+06	9e+06 | 
|...|
___

<a name="T8"></a>

#### 8. Создадим поле с категориями: Для фондов, которые инвестируют в 100 и более компаний, категорию `high_activity`. Для фондов, которые инвестируют в 20 и более компаний до 100, категорию `middle_activity`. Если количество инвестируемых компаний фонда не достигает 20 - `low_activity`. Отобразим все поля таблицы `fund` и новое поле с категориями.

```SQL
SELECT *,
       CASE
           WHEN invested_companies > 100 THEN 'high_activity'
           WHEN invested_companies >= 20
                AND invested_companies < 100 THEN 'middle_activity'
           WHEN invested_companies < 20 THEN 'low_activity'-- сюда запишите условия

       END
FROM fund;

```
Result: 
<table border="1">
<thead>
<tr><th>id</th><th> name</th><th> founded_at</th><th> domain</th><th> twitter_username</th><th> country_code</th><th> investment_rounds</th><th> invested_companies</th><th> milestones</th><th> created_at</th><th> updated_at</th><th> case</th></tr>
</thead>
<tbody>
<tr><td>1</td><td> Greylock Partners</td><td> 1965-01-01</td><td> greylock.com</td><td> greylockvc</td><td> USA</td><td> 307</td><td> 196</td><td> 0</td><td> 2007-05-25 20:18:23</td><td> 2012-12-27 00:42:24</td><td> high_activity</td></tr>
<tr><td>10</td><td> Mission Ventures</td><td> 1996-01-01</td><td> missionventures.com</td><td> </td><td> USA</td><td> 58</td><td> 33</td><td> 0</td><td> 2007-06-05 05:24:58</td><td> 2013-10-10 22:06:31</td><td> middle_activity</td></tr>
<tr><td>100</td><td> Kapor Enterprises Inc.</td><td> </td><td> kei.com</td><td> </td><td> USA</td><td> 2</td><td> 1</td><td> 0</td><td> 2007-07-12 09:42:21</td><td> 2008-11-21 05:41:53</td><td> low_activity</td></tr>
<tr><td>10000</td><td> 3x5 Special Opportunity Partners</td><td> </td><td> </td><td> </td><td> </td><td> 4</td><td> 4</td><td> 0</td><td> 2012-10-26 03:16:38</td><td> 2012-10-26 03:16:38</td><td> low_activity</td></tr>
<tr><td>10001</td><td> Salem Partners</td><td> 1997-01-01</td><td> salempartners.com</td><td> </td><td> USA</td><td> 1</td><td> 1</td><td> 0</td><td> 2012-10-26 03:16:38</td><td> 2013-05-24 14:07:03</td><td> low_activity</td></tr>
<tr><td>10002</td><td> 3T Capital 3tcapital.com</td><td> </td><td> </td><td> </td><td> FRA</td><td> 3</td><td> 3</td><td> 0</td><td> 2012-10-26 04:40:22</td><td> 2012-10-26 04:45:15</td><td> low_activity</td></tr>
<tr><td>10003</td><td> Merieux Developpement</td><td> 2009-01-01</td><td> merieux-developpement.com</td><td> </td><td> FRA</td><td> 2</td><td> 2</td><td> 0</td><td> 2012-10-26 10:38:51</td><td> 2013-05-17 05:43:09</td><td> low_activity</td></tr>
<tr><td>10004</td><td> Aquasourca aquasourca.com</td><td> </td><td> </td><td> </td><td> FRA</td><td> 1</td><td> 1</td><td> 0</td><td> 2012-10-26 10:38:51</td><td> 2013-06-07 11:05:22</td><td> low_activity</td></tr>
<tr><td>10005</td><td> Texas Entrepreneur Networks</td><td> 2009-01-01</td><td> texasenetworks.com</td><td> </td><td> USA</td><td> 1</td><td> 1</td><td> 0</td><td> 2012-10-26 11:51:12</td><td> 2013-06-07 11:07:44</td><td> low_activity</td></tr>
<tr><td>10008</td><td> Navitrio</td><td> </td><td> navitrio.com</td><td> </td><td> ISR</td><td> 1</td><td> 1</td><td> 0</td><td> 2012-10-27 03:59:33</td><td>2012-10-27 04:05:16</td><td> low_activity</td></tr>
<tr><td>1001</td><td> Burch Investment Group</td><td> </td><td> </td><td> </td><td> USA</td><td> 1</td><td> 1</td><td> 0</td><td> 2008-04-14 16:30:17</td><td> 2008-05-24 02:41:47</td><td> low_activity</td></tr>
</tbody>
</table>
<hr>

<a name="T9"></a>

#### 9. Для каждой из категорий, назначенных в предыдущем задании, посчитаем округлённое до ближайшего целого числа среднее количество инвестиционных раундов, в которых фонд принимал участие. Выведем на экран категории и среднее число инвестиционных раундов. Отсортируем таблицу по возрастанию среднего.

```SQL
SELECT ROUND(AVG(f.investment_rounds)),
       CASE
           WHEN invested_companies>=100 THEN 'high_activity'
           WHEN invested_companies>=20 THEN 'middle_activity'
           ELSE 'low_activity'
       END AS activity
FROM fund f
GROUP BY activity
ORDER BY round ASC;

```
Result: 
<table border="1">
<thead>
<tr><th>round</th><th>activity</th></tr>
</thead>
<tbody>
<tr><td>2</td><td>low_activity</td></tr>
<tr><td>51</td><td>middle_activity</td></tr>
<tr><td>252</td><td>high_activity</td></tr>
</tbody>
</table>
<hr>

<a name="T10"></a>

#### 10. Проанализируем, в каких странах находятся фонды, которые чаще всего инвестируют в стартапы. Для каждой страны посчитаем минимальное, максимальное и среднее число компаний, в которые инвестировали фонды этой страны, основанные с 2010 по 2012 год включительно. Исключим страны с фондами, у которых минимальное число компаний, получивших инвестиции, равно нулю. Выгрузите десять самых активных стран-инвесторов. Отсортируем таблицу по среднему количеству компаний от большего к меньшему, а затем по коду страны в лексикографическом порядке.

```SQL
SELECT country_code,
       MIN(f.invested_companies),
       MAX(f.invested_companies),
       AVG(f.invested_companies)
FROM fund f
WHERE EXTRACT(YEAR
              FROM f.founded_at::date) BETWEEN 2010 AND 2012
GROUP BY country_code
HAVING MIN(f.invested_companies) <> 0
ORDER BY AVG DESC, country_code ASC
LIMIT 10;;

```
Result: 
<table border="1">
<thead>
<tr><th>country_code</th><th>min</th><th>max</th><th>avg</th></tr>
</thead>
<tbody>
<tr><td>BGR</td><td>25</td><td>35</td><td>30</td></tr>
<tr><td>CHL</td><td>29</td><td>29</td><td>29</td></tr>
<tr><td>UKR</td><td>8</td><td>10</td><td>9</td></tr>
<tr><td>LTU</td><td>5</td><td>5</td><td>5</td></tr>
<tr><td>IRL</td><td>4</td><td>5</td><td>4.5</td></tr>
<tr><td>KEN</td><td>3</td><td>3</td><td>3</td></tr>
<tr><td>LBN</td><td>3</td><td>3</td><td>3</td></tr>
<tr><td>MUS</td><td>3</td><td>3</td><td>3</td></tr>
<tr><td>JPN</td><td>1</td><td>6</td><td>2.83333</td></tr>
<tr><td>HKG</td><td>2</td><td>3</td><td>2.66667</td></tr>
</tbody>
</table>
<hr>

<a name="T11"></a>
#### 11. Отобразим имя и фамилию всех сотрудников стартапов. Добавим поле с названием учебного заведения, которое окончил сотрудник, если эта информация известна.

```SQL
SELECT first_name, last_name, instituition
FROM people p
LEFT JOIN education ed ON p.id = ed.person_id;

```
Result: 
<table border="1">
<thead>
<tr><th>first_name</th><th> last_name</th><th> instituition</th></tr>
</thead>
<tbody>
<tr><td>John</td><td> Green</td><td> Washington University St. Louis</td></tr>
<tr><td>John</td><td> Green</td><td> Boston University</td></tr>
<tr><td>David</td><td> Peters</td><td> Rice University</td></tr>
<tr><td>Dan</td><td> Birdwhistell</td><td> University of Cambridge</td></tr>
<tr><td>Gal</td><td> Cohen</td><td> Tel Aviv University</td></tr>
<tr><td>Chris</td><td> Treadaway</td><td> University of Texas</td></tr>
<tr><td>Chris</td><td> Treadaway</td><td> Louisiana State University</td></tr>
<tr><td>Sam</td><td> Lessin</td><td> Harvard University</td></tr>
<tr><td>Guy</td><td> Levy-Yurista</td><td> University of Pennsylvania - The Wharton School</td></tr>
<tr><td>James</td><td> M.Butler</td><td> University of Maryland</td></tr>
<tr><td>...</td><td>...</td><td>...</td></tr>
</tbody>
</table>  
<hr>

<a name="T12"></a>

#### 12. Для каждой компании найдем количество учебных заведений, которые окончили её сотрудники. Выведем название компании и число уникальных названий учебных заведений. Составим топ-5 компаний по количеству университетов.

```SQL
SELECT c.name,
       COUNT(DISTINCT e.instituition) instituition_qty
FROM company c
JOIN people p ON c.id = p.company_id
JOIN education e ON p.id = e.person_id
GROUP BY c.name
ORDER BY instituition_qty DESC
LIMIT 5;

```
Result: 
<table border="1">
<thead>
<tr><th>name</th><th> instituition_qty</th></tr>
</thead>
<tbody>
<tr><td>Google</td><td> 167</td></tr>
<tr><td>Yahoo!</td><td> 115</td></tr>
<tr><td>Microsoft</td><td> 111</td></tr>
<tr><td>Knight Foundation</td><td> 74</td></tr>
<tr><td>Comcast</td><td> 66</td></tr>
</tbody>
</table>
<hr>

<a name="T13"></a>
#### 13. Составим список с уникальными названиями закрытых компаний, для которых первый раунд финансирования оказался последним.

```SQL
SELECT DISTINCT c.name
FROM company c
WHERE c.status = 'closed' AND c.id IN (
  SELECT fr.company_id
  FROM funding_round fr
  WHERE fr.is_first_round = 1 AND fr.is_last_round = 1
);

```
Result: 
<table border="1">
<thead>
<tr><th>name</th></tr>
</thead>
<tbody>
<tr><td>10BestThings</td></tr>
<tr><td>11i Solutions</td></tr>
<tr><td>169 ST.</td></tr>
<tr><td>1bib</td></tr>
<tr><td>1Cast</td></tr>
<tr><td>1DayMakeover</td></tr>
<tr><td>25eight</td></tr>
<tr><td>27 Perry</td></tr>
<tr><td>2Win-Solutions</td></tr>
<tr><td>3Touch</td></tr>
<tr><td>4Blox</td></tr>
<tr><td>51 Auto</td></tr>
<tr><td>77 Pieces</td></tr>
<tr><td>8aweek</td></tr>
<tr><td>...</td></tr>
</tbody>
</table>
<hr>

<a name="T14"></a>

#### 14. Составим список уникальных номеров сотрудников, которые работают в компаниях, отобранных в предыдущем задании.

```SQL
SELECT DISTINCT p.id
FROM people p
WHERE p.company_id IN (
  SELECT c.id
  FROM company c
  LEFT JOIN funding_round fr ON c.id = fr.company_id
  WHERE c.status = 'closed' AND fr.is_first_round = 1 AND fr.is_last_round = 1
);

```
Result: 
<table border="1">
<thead>
<tr><th>id</th></tr>
</thead>
<tbody>
<tr><td>62</td></tr>
<tr><td>97</td></tr>
<tr><td>98</td></tr>
<tr><td>225</td></tr>
<tr><td>226</td></tr>
<tr><td>227</td></tr>
<tr><td>281</td></tr>
<tr><td>282</td></tr>
<tr><td>...</td></tr>
</tbody>
</table>
<hr>

<a name="T15"></a>

#### 15. Составим таблицу, куда войдут уникальные пары с номерами сотрудников из предыдущей задачи и учебным заведением, которое окончил сотрудник.

```SQL
SELECT DISTINCT p.id, e.instituition
FROM people p
JOIN education e ON p.id = e.person_id
WHERE p.company_id IN (
  SELECT c.id
  FROM company c
  LEFT JOIN funding_round fr ON c.id = fr.company_id
  WHERE c.status = 'closed' AND fr.is_first_round = 1 AND fr.is_last_round = 1
);

```
Result: 
<table border="1">
<thead>
<tr><th>id</th><th>instituition</th></tr>
</thead>
<tbody>
<tr><td>349</td><td>AKI</td></tr>
<tr><td>349</td><td>ArtEZ Hogeschool voor de Kunsten</td></tr>
<tr><td>349</td><td>Rijks Akademie</td></tr>
<tr><td>699</td><td>Imperial College</td></tr>
<tr><td>779</td><td>Harvard University</td></tr>
<tr><td>779</td><td>Stanford University</td></tr>
<tr><td>968</td><td>University of Notre Dame</td></tr>
<tr><td>972</td><td>The University of Texas at Austin</td></tr>
<tr><td>1107</td><td>CDI, Sydney</td></tr>
<tr><td>1444</td><td>Brown University</td></tr>
<tr><td>1444</td><td>Massachusetts Institute of Technology (MIT)</td></tr>
<tr><td>...</td></tr>
</tbody>
</table>
<hr>

<a name="T16"></a>

#### 16. Посчитаем количество учебных заведений для каждого сотрудника из предыдущего задания. При подсчёте учитываем, что некоторые сотрудники могли окончить одно и то же заведение дважды.

```SQL
SELECT DISTINCT p.id, COUNT(e.instituition)
FROM people p
JOIN education e ON p.id = e.person_id
WHERE p.company_id IN (
  SELECT c.id
  FROM company c
  LEFT JOIN funding_round fr ON c.id = fr.company_id
  WHERE c.status = 'closed' AND fr.is_first_round = 1 AND fr.is_last_round = 1
)
GROUP BY p.id;

```
Result: 
<table border="1">
<thead>
<tr><th>id</th><th>count</th></tr>
</thead>
<tbody>
<tr><td>349</td><td>3</td></tr>
<tr><td>699</td><td>1</td></tr>
<tr><td>779</td><td>2</td></tr>
<tr><td>968</td><td>1</td></tr>
<tr><td>972</td><td>1</td></tr>
<tr><td>1107</td><td>1</td></tr>
<tr><td>1444</td><td>2</td></tr>
<tr><td>1833</td><td>1</td></tr>
<tr><td>1911</td><td>1</td></tr>
<tr><td>...</td></tr>
</tbody>
</table>
<hr>

<a name="T17"></a>

#### 17. Дополним предыдущий запрос и выведем среднее число учебных заведений (всех, не только уникальных), которые окончили сотрудники разных компаний.

```SQL
WITH base as (
SELECT DISTINCT p.id, COUNT(e.instituition)
FROM people p
JOIN education e ON p.id = e.person_id
WHERE p.company_id IN (
  SELECT c.id
  FROM company c
  LEFT JOIN funding_round fr ON c.id = fr.company_id
  WHERE c.status = 'closed' AND fr.is_first_round = 1 AND fr.is_last_round = 1
)
GROUP BY p.id)
SELECT AVG(COUNT)
FROM base;

```
Result: 
<table border="1">
<thead>
<tr><th>avg</th></tr>
</thead>
<tbody>
<tr><td>1.41509</td></tr>
</tbody>
</table>
<hr>

<a name="T18"></a>

#### 18. Напишем похожий запрос: выведем среднее число учебных заведений (всех, не только уникальных), которые окончили сотрудники Facebook.

```SQL
WITH base as (
SELECT DISTINCT p.id, COUNT(e.instituition)
FROM people p
JOIN education e ON p.id = e.person_id
WHERE p.company_id IN (
  SELECT c.id
  FROM company c
  LEFT JOIN funding_round fr ON c.id = fr.company_id
  WHERE c.name = 'Facebook'
)
GROUP BY p.id)
SELECT AVG(COUNT)
FROM base;

```
Result: 
<table border="1">
<thead>
<tr><th>avg</th></tr>
</thead>
<tbody>
<tr><td>1.51111</td></tr>
</tbody>
</table>
<hr>

<br><a name="T19"></a>
#### 19. Составим таблицу из полей: `name_of_fund` — название фонда; `name_of_company` — название компании; `amount` — сумма инвестиций, которую привлекла компания в раунде. В таблицу войдут данные о компаниях, в истории которых было больше шести важных этапов, а раунды финансирования проходили с 2012 по 2013 год включительно.

```SQL
SELECT f.name AS name_of_fund,
       c.name AS name_of_company,
       fr.raised_amount AS amount
FROM investment AS i
LEFT JOIN company AS c ON c.id = i.company_id
LEFT JOIN fund AS f ON i.fund_id = f.id
LEFT JOIN funding_round AS fr ON fr.id = i.funding_round_id
WHERE fr.funded_at BETWEEN '2012-01-01' AND '2013-12-31'
  AND c.milestones > 6;

```
Result: 
<table border="1">
<thead>
<tr><th>name_of_fund</th><th> name_of_company</th><th> amount</th></tr>
</thead>
<tbody>
<tr><td>Advance Publication</td><td> Gigya</td><td> 1.53e+07</td></tr>
<tr><td>Mayfield Fund</td><td> Gigya</td><td> 1.53e+07</td></tr>
<tr><td>Benchmark</td><td> Gigya</td><td> 1.53e+07</td></tr>
<tr><td>DAG Ventures</td><td> Gigya</td><td> 1.53e+07</td></tr>
<tr><td>Mitsui Global Investment</td><td> OpenX</td><td> 2.50112e+07</td></tr>
<tr><td>Accel Partners</td><td> OpenX</td><td> 2.50112e+07</td></tr>
<tr><td>Presidio Ventures</td><td> OpenX</td><td> 2.50112e+07</td></tr>
<tr><td>Index Ventures</td><td> OpenX</td><td> 2.50112e+07</td></tr>
<tr><td>...</td></tr>
</tbody>
</table>
<hr>


<br><a name="T20"></a>
#### 20. Выгрузим таблицу, в которой будут такие поля: название компании-покупателя; сумма сделки; название компании, которую купили; сумма инвестиций, вложенных в купленную компанию; доля, которая отображает, во сколько раз сумма покупки превысила сумму вложенных в компанию инвестиций, округлённая до ближайшего целого числа. Не учитывая те сделки, в которых сумма покупки равна нулю. Если сумма инвестиций в компанию равна нулю, исключим такую компанию из таблицы. Отсортируем таблицу по сумме сделки от большей к меньшей, а затем по названию купленной компании в лексикографическом порядке. Ограничим таблицу первыми десятью записями.

```SQL
SELECT acqn.buyer, acqn.buying_price, acqd.acquisition, acqd.investment_sum, ROUND(acqn.buying_price / acqd.investment_sum) increase
FROM (
  SELECT a.id id, c.name buyer, a.price_amount buying_price
  FROM acquisition a
  LEFT JOIN company c ON a.acquiring_company_id = c.id
  WHERE a.price_amount > 0
) acqn
JOIN (
  SELECT a.id as id, c.name acquisition, c.funding_total investment_sum
  FROM acquisition a
  LEFT JOIN company c ON a.acquired_company_id = c.id
  WHERE c.funding_total > 0
) acqd ON acqn.id = acqd.id
ORDER BY buying_price DESC, acquisition
LIMIT 10;

```
Result: 
<table border="1">
<thead>
<tr><th>buyer</th><th> buying_price</th><th> acquisition</th><th> investment_sum</th><th> increase</th></tr>
</thead>
<tbody>
<tr><td>Microsoft</td><td> 8.5e+09</td><td> Skype</td><td> 7.6805e+07</td><td> 111</td></tr>
<tr><td>Scout Labs</td><td> 4.9e+09</td><td> Varian Semiconductor Equipment Associates</td><td> 4.8e+06</td><td> 1021</td></tr>
<tr><td>Broadcom</td><td> 3.7e+09</td><td> Aeluros</td><td> 7.97e+06</td><td> 464</td></tr>
<tr><td>Broadcom</td><td> 3.7e+09</td><td> NetLogic Microsystems</td><td> 1.88527e+08</td><td> 20</td></tr>
<tr><td>Level 3 Communications</td><td> 3e+09</td><td> Global Crossing</td><td> 4.1e+07</td><td> 73</td></tr>
<tr><td>Yahoo!</td><td> 2.87e+09</td><td> GeoCities</td><td> 4e+07</td><td> 72</td></tr>
<tr><td>eBay</td><td> 2.6e+09</td><td> Skype</td><td> 7.6805e+07</td><td> 34</td></tr>
<tr><td>Salesforce</td><td> 2.5e+09</td><td> ExactTarget</td><td> 2.3821e+08</td><td> 10</td></tr>
<tr><td>Johnson & Johnson</td><td> 2.3e+09</td><td> Crucell</td><td> 4.43e+08</td><td> 5</td></tr>
<tr><td>IAC</td><td> 1.85e+09</td><td> Ask.com</td><td> 2.5e+07</td><td> 74</td></tr>
</tbody>
</table>
<hr>

<br><a name="T21"></a>
#### 21. Выгрузим таблицу, в которую войдут названия компаний из категории `social`, получившие финансирование с 2010 по 2013 год включительно. Проверим, что сумма инвестиций не равна нулю. Выведем также номер месяца, в котором проходил раунд финансирования.

```SQL
SELECT c.name,
       EXTRACT(MONTH
               FROM fr.funded_at::date)
FROM company c
LEFT JOIN funding_round fr ON c.id = fr.company_id
WHERE c.category_code = 'social'
  AND fr.raised_amount <> 0
  AND EXTRACT(YEAR
              FROM fr.funded_at::date) BETWEEN '2010' AND '2013';

```
Result: 
<table border="1">
<thead>
<tr><th>name</th><th> date_part</th></tr>
</thead>
<tbody>
<tr><td>Klout</td><td> 1</td></tr>
<tr><td>WorkSimple</td><td> 3</td></tr>
<tr><td>HengZhi</td><td> 1</td></tr>
<tr><td>Twitter</td><td> 1</td></tr>
<tr><td>SocialGO</td><td> 1</td></tr>
<tr><td>ThisNext</td><td> 1</td></tr>
<tr><td>Tagged</td><td> 1</td></tr>
<tr><td>LikeMe.Net</td><td> 2</td></tr>
<tr><td>Busuu</td><td> 10</td></tr>
<tr><td>NetBase Solutions</td><td> 3</td></tr>
<tr><td>ShopIgniter</td><td> 3</td></tr>
<tr><td>...</td></tr>
</tbody>
</table>
<hr>

<br><a name="T22"></a>
#### 22. Отберем данные по месяцам с 2010 по 2013 год, когда проходили инвестиционные раунды. Сгруппируем данные по номеру месяца и получим таблицу, в которой будут поля: номер месяца, в котором проходили раунды; количество уникальных названий фондов из США, которые инвестировали в этом месяце; количество компаний, купленных за этот месяц; общая сумма сделок по покупкам в этом месяце.

```SQL
WITH fundings AS
  (SELECT EXTRACT(MONTH
                  FROM fr.funded_at::date) AS funding_month,
          COUNT(DISTINCT f.id) funds
   FROM fund f
   LEFT JOIN investment i ON f.id = i.fund_id
   LEFT JOIN funding_round fr ON i.funding_round_id = fr.id
   WHERE f.country_code = 'USA'
     AND EXTRACT(YEAR
                 FROM fr.funded_at::date) BETWEEN 2010 AND 2013
   GROUP BY funding_month),
     acquisitions AS
  (SELECT EXTRACT(MONTH
                  FROM acquired_at::date) AS funding_month,
          COUNT(acquired_company_id) purchased,
          SUM(price_amount) sum_total
   FROM acquisition
   WHERE EXTRACT(YEAR
                 FROM acquired_at::DATE) BETWEEN 2010 AND 2013
        GROUP BY funding_month)
SELECT fnd.funding_month,
       fnd.funds,
       ac.purchased,
       ac.sum_total
FROM fundings fnd
LEFT JOIN acquisitions ac ON fnd.funding_month = ac.funding_month;

```
Result: 
<table border="1">
<thead>
<tr><th>funding_month</th><th>funds</th><th>purchased</th><th>sum_total</th></tr>
</thead>
<tbody>
<tr><td>1</td><td>815</td><td>600</td><td>2.71083e+10</td></tr>
<tr><td>2</td><td>637</td><td>418</td><td>4.13903e+10</td></tr>
<tr><td>3</td><td>695</td><td>458</td><td>5.95016e+10</td></tr>
<tr><td>4</td><td>718</td><td>411</td><td>3.03837e+10</td></tr>
<tr><td>5</td><td>695</td><td>532</td><td>8.60122e+10</td></tr>
<tr><td>6</td><td>785</td><td>525</td><td>5.20883e+10</td></tr>
<tr><td>7</td><td>803</td><td>488</td><td>4.98541e+10</td></tr>
<tr><td>8</td><td>726</td><td>454</td><td>7.77093e+10</td></tr>
<tr><td>...</td></tr>
</tbody>
</table>
<hr>

<br><a name="T23"></a>
#### 23. Составим сводную таблицу и выведем среднюю сумму инвестиций для стран, в которых есть стартапы, зарегистрированные в 2011, 2012 и 2013 годах. Данные за каждый год должны быть в отдельном поле. Отсортируем таблицу по среднему значению инвестиций за 2011 год от большего к меньшему.

```SQL
SELECT y_11.country, Y_2011, Y_2012, Y_2013
FROM (
  SELECT country_code country,
  AVG(funding_total) Y_2011
  FROM company
  WHERE EXTRACT(YEAR FROM founded_at::DATE) = '2011'
  GROUP BY country
) y_11
JOIN (
  SELECT country_code country,
  AVG(funding_total) Y_2012
  FROM company
  WHERE EXTRACT(YEAR FROM founded_at::DATE) = '2012'
  GROUP BY country
) y_12 ON y_11.country = y_12.country
JOIN (
  SELECT country_code country,
  AVG(funding_total) Y_2013
  FROM company
  WHERE EXTRACT(YEAR FROM founded_at::DATE) = '2013'
  GROUP BY country
) y_13 ON y_12.country = y_13.country
ORDER BY y_11.y_2011 DESC;

```
Result: 
<table border="1">
<thead>
<tr><th>country</th><th>y_2011</th><th>y_2012</th><th>y_2013</th></tr>
</thead>
<tbody>
<tr><td>PER</td><td>4e+06</td><td>41000</td><td>25000</td></tr>
<tr><td>USA</td><td>2.24396e+06</td><td>1.20671e+06</td><td>1.09336e+06</td></tr>
<tr><td>HKG</td><td>2.18078e+06</td><td>226227</td><td>0</td></tr>
<tr><td>PHL</td><td>1.75e+06</td><td>4218.75</td><td>2500</td></tr>
<tr><td>ARE</td><td>1.718e+06</td><td>197222</td><td>35333.3</td></tr>
<tr><td>JPN</td><td>1.66431e+06</td><td>674720</td><td>50000</td></tr>
<tr><td>AUT</td><td>1.5342e+06</td><td>147806</td><td>85773.3</td></tr>
<tr><td>BRA</td><td>1.38007e+06</td><td>240639</td><td>67944.4</td></tr>
<tr><td>DEU</td><td>1.1288e+06</td><td>1.32915e+06</td><td>66612.7</td></tr>
<tr><td>ISR</td><td>1.03076e+06</td><td>1.27121e+06</td><td>294022</td></tr>
<tr><td>PST</td><td>1e+06</td><td>0</td><td>0</td></tr>
<tr><td>FRA</td><td>977874</td><td>291227</td><td>642083</td></tr>
<tr><td>CHN</td><td>975918</td><td>611436</td><td>1e+06</td></tr>
<tr><td>AUS</td><td>963088</td><td>192949</td><td>26313.7</td></tr>
<tr><td>...</td></tr>
</tbody>
</table>
<hr>
