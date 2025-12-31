# PostgreSQL Queries: Startup Investment Analysis

![Database Schema](https://user-images.githubusercontent.com/114194866/211474878-3c01071d-310b-486f-88c4-a732c94deb3d.png)

## Project Overview

This project analyzes venture capital investment data using PostgreSQL, demonstrating proficiency in complex query design, data aggregation, and multi-table joins. The analysis extracts insights from a startup investments database based on the Kaggle Startup Investments dataset.

## Database Schema Description

The database contains seven interconnected tables tracking the venture capital ecosystem:

### Core Tables

**`company`** - Central table storing startup information including:
- Company identification (id, name)
- Classification data (category_code, status, country_code)
- Financial metrics (funding_total, investment_rounds, funding_rounds)
- Temporal data (founded_at, closed_at)
- Social media presence (domain, twitter_username)

**`funding_round`** - Records individual investment rounds with:
- Round identification and classification (id, funding_round_type)
- Financial details (raised_amount, pre_money_valuation)
- Round characteristics (is_first_round, is_last_round)
- Participants and timing (participants, funded_at)

**`investment`** - Junction table linking funds to specific funding rounds:
- Foreign key relationships (funding_round_id, company_id, fund_id)
- Temporal tracking (created_at, updated_at)

**`fund`** - Venture capital fund information:
- Fund identification (id, name)
- Geographic data (country_code)
- Investment activity metrics (investment_rounds, invested_companies, milestones)
- Temporal data (founded_at)

### Supporting Tables

**`people`** - Individual professionals in the ecosystem:
- Personal identification (id, first_name, last_name)
- Professional affiliation (company_id)
- Social media (twitter_username)

**`education`** - Educational background of professionals:
- Academic credentials (person_id, degree_type, institution)
- Completion data (graduated_at)

**`acquisition`** - Company acquisition transactions:
- Transaction parties (acquiring_company_id, acquired_company_id)
- Financial terms (price_amount, term_code)
- Transaction timing (acquired_at)

### Relationships

- Companies participate in multiple funding_rounds (one-to-many)
- Funding_rounds attract investments from multiple funds (many-to-many via investment table)
- Funds invest in multiple companies across various rounds (many-to-many)
- People are associated with companies and have educational histories (one-to-many)
- Companies can acquire or be acquired by other companies (self-referential through acquisition table)

Understanding this schema is essential for navigating investment market data, as it captures the complete lifecycle of venture-backed companies from founding through funding rounds to potential acquisitions.

---

## Table of Contents

1. [Count Closed Companies](#query-1)
2. [US News Company Funding](#query-2)
3. [Acquisition Transaction Totals](#query-3)
4. [Twitter Account Analysis - 'Silver' Pattern](#query-4)
5. [Twitter Account Analysis - 'money' & 'K' Pattern](#query-5)
6. [Investment by Country](#query-6)
7. [Funding Round Statistics by Date](#query-7)
8. [Fund Activity Categorization](#query-8)
9. [Average Investment Rounds by Category](#query-9)
10. [Geographic Analysis of Active Funds](#query-10)
11. [Employee Name Listing](#query-11)
12. [Educational Institution Analysis](#query-12)
13. [Failed Companies - Single Round Analysis](#query-13)
14. [Employee IDs from Failed Companies](#query-14)
15. [Employee-Education Pairing](#query-15)
16. [Educational Institutions per Employee](#query-16)
17. [Average Education Metrics by Company](#query-17)
18. [Facebook Employee Education Analysis](#query-18)
19. [Fund-Company Investment Matrix](#query-19)
20. [Acquisition ROI Analysis](#query-20)
21. [Social Category Funding Timeline](#query-21)
22. [Monthly Investment Patterns (2010-2013)](#query-22)
23. [Yearly Funding Averages by Country](#query-23)

---

<a name="query-1"></a>
## Query 1: Count Closed Companies

**Objective:** Calculate the total number of companies with 'closed' status.

**SQL Techniques:** Basic filtering with WHERE clause

```sql
SELECT COUNT(c.status)
FROM company c
WHERE c.status = 'closed';
```

**Result:**
| count |
|-------|
| 2584  |

**[⬆ Back to Top](#table-of-contents)**

---

<a name="query-2"></a>
## Query 2: US News Company Funding

**Objective:** Display funding amounts for US-based news companies, sorted by funding total in descending order.

**SQL Techniques:** Numeric formatting with TO_CHAR, compound WHERE conditions, ORDER BY

```sql
SELECT to_char(CAST(c.funding_total AS numeric), '9999999999FM') AS funding_total
FROM company c
WHERE c.category_code = 'news'
  AND country_code = 'USA'
ORDER BY c.funding_total DESC;
```

**Result:**
| funding_total |
|---------------|
| 622553000     |
| 250000000     |
| 160500000     |
| 128000000     |
| 126500000     |
| 70000000      |
| ...           |

**[⬆ Back to Top](#table-of-contents)**

---

<a name="query-3"></a>
## Query 3: Acquisition Transaction Totals

**Objective:** Calculate total cash acquisition value for transactions between 2011-2013.

**SQL Techniques:** SUM aggregation, EXTRACT function for date filtering, multi-year IN clause

```sql
SELECT SUM(a.price_amount)
FROM acquisition a
WHERE a.term_code = 'cash'
  AND EXTRACT(YEAR FROM acquired_at::date) IN (2011, 2012, 2013);
```

**Result:**
| sum          |
|--------------|
| 1.37762e+11  |

**[⬆ Back to Top](#table-of-contents)**

---

<a name="query-4"></a>
## Query 4: Twitter Account Analysis - 'Silver' Pattern

**Objective:** Find people whose Twitter usernames begin with 'Silver'.

**SQL Techniques:** Pattern matching with LIKE operator

```sql
SELECT p.first_name,
       p.last_name,
       p.twitter_username
FROM people p
WHERE p.twitter_username LIKE 'Silver%';
```

**Result:**
| first_name | last_name | twitter_username |
|------------|-----------|------------------|
| Rebecca    | Silver    | SilverRebecca    |
| Silver     | Teede     | SilverMatrixx    |
| Mattias    | Guilotte  | Silverreven      |

**[⬆ Back to Top](#table-of-contents)**

---

<a name="query-5"></a>
## Query 5: Twitter Account Analysis - 'money' & 'K' Pattern

**Objective:** Find people whose Twitter usernames contain 'money' AND whose last names begin with 'K'.

**SQL Techniques:** Multiple pattern matching with LIKE, compound AND conditions

```sql
SELECT *
FROM people p
WHERE p.twitter_username LIKE '%money%'
  AND p.last_name LIKE 'K%';
```

**Result:**
| id    | first_name | last_name | company_id | twitter_username | created_at          | updated_at          |
|-------|------------|-----------|------------|------------------|---------------------|---------------------|
| 63081 | Gregory    | Kim       |            | gmoney75         | 2010-07-13 03:46:28 | 2011-12-12 22:01:34 |

**[⬆ Back to Top](#table-of-contents)**

---

<a name="query-6"></a>
## Query 6: Investment by Country

**Objective:** Aggregate total funding by country code, sorted in descending order.

**SQL Techniques:** GROUP BY aggregation, SUM function, ordering by aggregate values

```sql
SELECT c.country_code,
       SUM(c.funding_total)
FROM company c
GROUP BY c.country_code
ORDER BY SUM(c.funding_total) DESC;
```

**Result:**
| country_code | sum         |
|--------------|-------------|
| USA          | 31058800000 |
| GBR          | 1770560000  |
|              | 1085590000  |
| CHN          | 1068970000  |
| CAN          | 1058470000  |
| IND          | 1049970000  |
| DEU          | 1038100000  |
| FRA          | 614141000   |
| ISR          | 514199000   |
| CHE          | 424152300   |
| ...          | ...         |

**[⬆ Back to Top](#table-of-contents)**

---

<a name="query-7"></a>
## Query 7: Funding Round Statistics by Date

**Objective:** Show min/max raised amounts by funding date, excluding zero minimums and dates where min equals max.

**SQL Techniques:** GROUP BY with multiple aggregates (MIN, MAX), HAVING clause with compound conditions

```sql
SELECT fr.funded_at,
       MIN(fr.raised_amount),
       MAX(fr.raised_amount)
FROM funding_round fr
GROUP BY fr.funded_at
HAVING MIN(fr.raised_amount) <> 0
  AND MIN(fr.raised_amount) <> MAX(fr.raised_amount);
```

**Result:**
| funded_at  | min       | max       |
|------------|-----------|-----------|
| 2012-08-22 | 40000     | 7.5e+07   |
| 2010-07-25 | 3.27825e+06 | 9e+06   |
| 2002-03-01 | 2.84418e+06 | 8.95915e+06 |
| 2010-10-11 | 28000     | 2e+08     |
| 2007-01-18 | 5.5e+06   | 2.3e+07   |
| ...        | ...       | ...       |

**[⬆ Back to Top](#table-of-contents)**

---

<a name="query-8"></a>
## Query 8: Fund Activity Categorization

**Objective:** Categorize funds by investment activity level (high/middle/low) based on number of invested companies.

**SQL Techniques:** CASE WHEN conditional logic, computed columns

```sql
SELECT *,
       CASE
           WHEN invested_companies >= 100 THEN 'high_activity'
           WHEN invested_companies >= 20 THEN 'middle_activity'
           ELSE 'low_activity'
       END AS activity
FROM fund;
```

**Result:**
| id | name              | founded_at | domain | twitter_username | country_code | investment_rounds | invested_companies | milestones | created_at          | updated_at          | activity        |
|----|-------------------|------------|--------|------------------|--------------|-------------------|--------------------|------------|---------------------|---------------------|-----------------|
| 1  | Greylock Partners | 1965-01-01 |        |                  | USA          | 164               | 133                |            | 2007-06-01 18:19:34 | 2013-12-03 19:50:23 | high_activity   |
| 2  | Sequoia Capital   | 1972-01-01 |        |                  | USA          | 205               | 167                |            | 2007-06-01 18:19:35 | 2013-10-23 16:42:10 | high_activity   |
| ... | ...             | ...        | ...    | ...              | ...          | ...               | ...                | ...        | ...                 | ...                 | ...             |

**[⬆ Back to Top](#table-of-contents)**

---

<a name="query-9"></a>
## Query 9: Average Investment Rounds by Category

**Objective:** Calculate rounded average investment rounds for each fund activity category.

**SQL Techniques:** Subquery with CASE logic, AVG function, ROUND function, GROUP BY on computed field

```sql
SELECT CASE
           WHEN invested_companies >= 100 THEN 'high_activity'
           WHEN invested_companies >= 20 THEN 'middle_activity'
           ELSE 'low_activity'
       END AS activity,
       ROUND(AVG(investment_rounds))
FROM fund
GROUP BY activity
ORDER BY ROUND(AVG(investment_rounds));
```

**Result:**
| activity        | round |
|-----------------|-------|
| low_activity    | 2     |
| middle_activity | 35    |
| high_activity   | 102   |

**[⬆ Back to Top](#table-of-contents)**

---

<a name="query-10"></a>
## Query 10: Geographic Analysis of Active Funds

**Objective:** Identify countries with the most active investment funds (top 10).

**SQL Techniques:** COUNT aggregation, GROUP BY, ORDER BY DESC, LIMIT clause

```sql
SELECT f.country_code,
       MIN(f.invested_companies),
       MAX(f.invested_companies),
       AVG(f.invested_companies)
FROM fund f
WHERE EXTRACT(YEAR FROM f.founded_at::date) BETWEEN '2010' AND '2012'
GROUP BY f.country_code
HAVING MIN(f.invested_companies) > 0
ORDER BY AVG(f.invested_companies) DESC,
         f.country_code
LIMIT 10;
```

**Result:**
| country_code | min | max | avg     |
|--------------|-----|-----|---------|
| NOR          | 7   | 7   | 7       |
| SGP          | 2   | 6   | 4       |
| USA          | 1   | 57  | 3.74545 |
| ISR          | 1   | 3   | 2       |
| FRA          | 2   | 2   | 2       |
| IRL          | 2   | 2   | 2       |
| ...          | ... | ... | ...     |

**[⬆ Back to Top](#table-of-contents)**

---

<a name="query-11"></a>
## Query 11: Employee Name Listing

**Objective:** Retrieve names of all employees working at startups, including company affiliation.

**SQL Techniques:** INNER JOIN, CONCAT for name formatting, distinct company filtering

```sql
SELECT CONCAT(p.first_name, ' ', p.last_name) AS name,
       c.name AS company_name
FROM people p
INNER JOIN company c ON p.company_id = c.id;
```

**Result:**
| name            | company_name  |
|-----------------|---------------|
| Jawed Karim     | YouTube       |
| Chad Hurley     | YouTube       |
| Steve Chen      | YouTube       |
| John Doerr      | Kleiner Perkins Caufield & Byers |
| David Filo      | Yahoo!        |
| ...             | ...           |

**[⬆ Back to Top](#table-of-contents)**

---

<a name="query-12"></a>
## Query 12: Educational Institution Analysis

**Objective:** Count unique educational institutions attended by employees at each company.

**SQL Techniques:** Three-table JOIN, COUNT DISTINCT, GROUP BY company

```sql
SELECT c.name,
       COUNT(DISTINCT e.instituition)
FROM company c
JOIN people p ON c.id = p.company_id
JOIN education e ON p.id = e.person_id
GROUP BY c.name
ORDER BY COUNT(DISTINCT e.instituition) DESC;
```

**Result:**
| name            | count |
|-----------------|-------|
| Technorati      | 7     |
| Digg            | 6     |
| NewsGator       | 5     |
| LinkedIn        | 5     |
| Yahoo!          | 5     |
| ...             | ...   |

**[⬆ Back to Top](#table-of-contents)**

---

<a name="query-13"></a>
## Query 13: Failed Companies - Single Round Analysis

**Objective:** Identify closed companies where the first funding round was also the last.

**SQL Techniques:** Subquery filtering, boolean flag evaluation (is_first_round, is_last_round)

```sql
SELECT DISTINCT c.name
FROM company c
WHERE c.status = 'closed'
  AND c.id IN (
      SELECT company_id
      FROM funding_round
      WHERE is_first_round = 1 AND is_last_round = 1
  );
```

**Result:**
| name                |
|---------------------|
| DailyStory Media    |
| InterNoded          |
| TrueU               |
| Social Concepts     |
| Wink                |
| Cuil                |
| ...                 |

**[⬆ Back to Top](#table-of-contents)**

---

<a name="query-14"></a>
## Query 14: Employee IDs from Failed Companies

**Objective:** List unique employee IDs from companies that closed after a single funding round.

**SQL Techniques:** Nested subqueries, DISTINCT, multi-level IN clauses

```sql
SELECT DISTINCT p.id
FROM people p
WHERE p.company_id IN (
    SELECT c.id
    FROM company c
    WHERE c.status = 'closed'
      AND c.id IN (
          SELECT company_id
          FROM funding_round
          WHERE is_first_round = 1 AND is_last_round = 1
      )
);
```

**Result:**
| id    |
|-------|
| 22756 |
| 24192 |
| 44226 |
| 39591 |
| 52167 |
| ...   |

**[⬆ Back to Top](#table-of-contents)**

---

<a name="query-15"></a>
## Query 15: Employee-Education Pairing

**Objective:** Create unique pairs of employee IDs and their educational institutions for employees from single-round failed companies.

**SQL Techniques:** Four-table chain with nested subqueries, DISTINCT pairs

```sql
SELECT DISTINCT p.id,
                e.instituition
FROM people p
LEFT JOIN education e ON p.id = e.person_id
WHERE p.company_id IN (
    SELECT c.id
    FROM company c
    WHERE c.status = 'closed'
      AND c.id IN (
          SELECT company_id
          FROM funding_round
          WHERE is_first_round = 1 AND is_last_round = 1
      )
)
AND e.instituition IS NOT NULL;
```

**Result:**
| id    | instituition               |
|-------|----------------------------|
| 22756 | Stanford University        |
| 24192 | University of Pennsylvania |
| 44226 | Harvard Business School    |
| 39591 | Northwestern University    |
| 52167 | Stanford University        |
| ...   | ...                        |

**[⬆ Back to Top](#table-of-contents)**

---

<a name="query-16"></a>
## Query 16: Educational Institutions per Employee

**Objective:** Count how many institutions each employee from failed single-round companies attended.

**SQL Techniques:** Subquery, COUNT aggregation, GROUP BY person

```sql
SELECT p.id,
       COUNT(e.instituition)
FROM people p
LEFT JOIN education e ON p.id = e.person_id
WHERE p.company_id IN (
    SELECT c.id
    FROM company c
    WHERE c.status = 'closed'
      AND c.id IN (
          SELECT company_id
          FROM funding_round
          WHERE is_first_round = 1 AND is_last_round = 1
      )
)
AND e.instituition IS NOT NULL
GROUP BY p.id;
```

**Result:**
| id    | count |
|-------|-------|
| 22756 | 1     |
| 24192 | 2     |
| 44226 | 1     |
| 39591 | 1     |
| 52167 | 1     |
| ...   | ...   |

**[⬆ Back to Top](#table-of-contents)**

---

<a name="query-17"></a>
## Query 17: Average Education Metrics by Company

**Objective:** Calculate average number of educational institutions per employee, grouped by company, including all institution entries (not just unique).

**SQL Techniques:** Complex subquery with COUNT, nested GROUP BY, AVG of aggregated counts

```sql
SELECT AVG(cnt) AS avg_count
FROM (
    SELECT p.id,
           COUNT(e.instituition) AS cnt
    FROM people p
    LEFT JOIN education e ON p.id = e.person_id
    WHERE p.company_id IN (
        SELECT c.id
        FROM company c
        WHERE c.status = 'closed'
          AND c.id IN (
              SELECT company_id
              FROM funding_round
              WHERE is_first_round = 1 AND is_last_round = 1
          )
    )
    AND e.instituition IS NOT NULL
    GROUP BY p.id
) AS counts;
```

**Result:**
| avg_count        |
|------------------|
| 1.08333333333333 |

**[⬆ Back to Top](#table-of-contents)**

---

<a name="query-18"></a>
## Query 18: Facebook Employee Education Analysis

**Objective:** Calculate average educational institutions per Facebook employee (all entries, not unique).

**SQL Techniques:** Filtered subquery, COUNT with company-specific WHERE clause, nested aggregation

```sql
SELECT AVG(cnt) AS avg_count
FROM (
    SELECT p.id,
           COUNT(e.instituition) AS cnt
    FROM people p
    RIGHT JOIN education e ON p.id = e.person_id
    WHERE p.company_id IN (
        SELECT c.id
        FROM company c
        WHERE c.name = 'Facebook'
    )
    GROUP BY p.id
) AS counts;
```

**Result:**
| avg_count        |
|------------------|
| 1.27272727272727 |

**[⬆ Back to Top](#table-of-contents)**

---

<a name="query-19"></a>
## Query 19: Fund-Company Investment Matrix

**Objective:** Create detailed investment matrix showing fund name, company name, and investment amount for each funding round.

**SQL Techniques:** Four-table JOIN across fund → investment → funding_round → company

```sql
SELECT f.name AS name_of_fund,
       c.name AS name_of_company,
       fr.raised_amount AS amount
FROM investment i
LEFT JOIN company c ON i.company_id = c.id
LEFT JOIN fund f ON i.fund_id = f.id
LEFT JOIN funding_round fr ON i.funding_round_id = fr.id
WHERE c.milestones > 6
  AND EXTRACT(YEAR FROM fr.funded_at::date) BETWEEN '2012' AND '2013'
ORDER BY fr.raised_amount DESC;
```

**Result:**
| name_of_fund           | name_of_company | amount    |
|------------------------|-----------------|-----------|
| Summit Partners        | Groupon         | 9.5e+08   |
| General Catalyst Partners | Groupon      | 9.5e+08   |
| Battery Ventures       | Groupon         | 9.5e+08   |
| New Enterprise Associates | Groupon      | 9.5e+08   |
| Accel Partners         | Groupon         | 9.5e+08   |
| ...                    | ...             | ...       |

**[⬆ Back to Top](#table-of-contents)**

---

<a name="query-20"></a>
## Query 20: Acquisition ROI Analysis

**Objective:** Analyze acquisition returns by comparing purchase price to prior investment, calculating ROI multiplier.

**SQL Techniques:** Self-join with subqueries, ROUND function, complex filtering on both price and investment amounts, LIMIT

```sql
SELECT acqn.buyer,
       acqn.buying_price,
       acqd.acquisition,
       acqd.investment_sum,
       ROUND(acqn.buying_price / acqd.investment_sum) AS increase
FROM (
    SELECT a.id AS id,
           c.name AS buyer,
           a.price_amount AS buying_price
    FROM acquisition a
    LEFT JOIN company c ON a.acquiring_company_id = c.id
    WHERE a.price_amount > 0
) acqn
JOIN (
    SELECT a.id AS id,
           c.name AS acquisition,
           c.funding_total AS investment_sum
    FROM acquisition a
    LEFT JOIN company c ON a.acquired_company_id = c.id
    WHERE c.funding_total > 0
) acqd ON acqn.id = acqd.id
ORDER BY buying_price DESC, acquisition
LIMIT 10;
```

**Result:**
| buyer                    | buying_price | acquisition                              | investment_sum | increase |
|--------------------------|--------------|------------------------------------------|----------------|----------|
| Microsoft                | 8.5e+09      | Skype                                    | 7.6805e+07     | 111      |
| Scout Labs               | 4.9e+09      | Varian Semiconductor Equipment Associates | 4.8e+06        | 1021     |
| Broadcom                 | 3.7e+09      | Aeluros                                  | 7.97e+06       | 464      |
| Broadcom                 | 3.7e+09      | NetLogic Microsystems                    | 1.88527e+08    | 20       |
| Level 3 Communications   | 3e+09        | Global Crossing                          | 4.1e+07        | 73       |
| Yahoo!                   | 2.87e+09     | GeoCities                                | 4e+07          | 72       |
| eBay                     | 2.6e+09      | Skype                                    | 7.6805e+07     | 34       |
| Salesforce               | 2.5e+09      | ExactTarget                              | 2.3821e+08     | 10       |
| Johnson & Johnson        | 2.3e+09      | Crucell                                  | 4.43e+08       | 5        |
| IAC                      | 1.85e+09     | Ask.com                                  | 2.5e+07        | 74       |

**[⬆ Back to Top](#table-of-contents)**

---

<a name="query-21"></a>
## Query 21: Social Category Funding Timeline

**Objective:** Track funding rounds for social media companies between 2010-2013, showing month of funding.

**SQL Techniques:** LEFT JOIN, EXTRACT for month parsing, date range filtering with BETWEEN, non-zero amount validation

```sql
SELECT c.name,
       EXTRACT(MONTH FROM fr.funded_at::date)
FROM company c
LEFT JOIN funding_round fr ON c.id = fr.company_id
WHERE c.category_code = 'social'
  AND fr.raised_amount <> 0
  AND EXTRACT(YEAR FROM fr.funded_at::date) BETWEEN '2010' AND '2013';
```

**Result:**
| name              | date_part |
|-------------------|-----------|
| Klout             | 1         |
| WorkSimple        | 3         |
| HengZhi           | 1         |
| Twitter           | 1         |
| SocialGO          | 1         |
| ThisNext          | 1         |
| Tagged            | 1         |
| LikeMe.Net        | 2         |
| Busuu             | 10        |
| NetBase Solutions | 3         |
| ShopIgniter       | 3         |
| ...               | ...       |

**[⬆ Back to Top](#table-of-contents)**

---

<a name="query-22"></a>
## Query 22: Monthly Investment Patterns (2010-2013)

**Objective:** Comprehensive monthly analysis combining funding activity and acquisition data, showing number of unique US funds, companies purchased, and total acquisition value per month.

**SQL Techniques:** Common Table Expressions (WITH clause), parallel CTEs, LEFT JOIN of aggregated results, COUNT DISTINCT for unique funds

```sql
WITH fundings AS (
    SELECT EXTRACT(MONTH FROM fr.funded_at::date) AS funding_month,
           COUNT(DISTINCT f.id) AS funds
    FROM fund f
    LEFT JOIN investment i ON f.id = i.fund_id
    LEFT JOIN funding_round fr ON i.funding_round_id = fr.id
    WHERE f.country_code = 'USA'
      AND EXTRACT(YEAR FROM fr.funded_at::date) BETWEEN 2010 AND 2013
    GROUP BY funding_month
),
acquisitions AS (
    SELECT EXTRACT(MONTH FROM acquired_at::date) AS funding_month,
           COUNT(acquired_company_id) AS purchased,
           SUM(price_amount) AS sum_total
    FROM acquisition
    WHERE EXTRACT(YEAR FROM acquired_at::date) BETWEEN 2010 AND 2013
    GROUP BY funding_month
)
SELECT fnd.funding_month,
       fnd.funds,
       ac.purchased,
       ac.sum_total
FROM fundings fnd
LEFT JOIN acquisitions ac ON fnd.funding_month = ac.funding_month;
```

**Result:**
| funding_month | funds | purchased | sum_total   |
|---------------|-------|-----------|-------------|
| 1             | 815   | 600       | 2.71083e+10 |
| 2             | 637   | 418       | 4.13903e+10 |
| 3             | 695   | 458       | 5.95016e+10 |
| 4             | 718   | 411       | 3.03837e+10 |
| 5             | 695   | 532       | 8.60122e+10 |
| 6             | 785   | 525       | 5.20883e+10 |
| 7             | 803   | 488       | 4.98541e+10 |
| 8             | 726   | 454       | 7.77093e+10 |
| ...           | ...   | ...       | ...         |

**[⬆ Back to Top](#table-of-contents)**

---

<a name="query-23"></a>
## Query 23: Yearly Funding Averages by Country

**Objective:** Create pivot-style analysis showing average funding per country across three years (2011-2013) in separate columns.

**SQL Techniques:** Multiple subquery JOINs, AVG aggregation per year, self-joining on country_code to create pivot structure

```sql
SELECT y_11.country,
       Y_2011,
       Y_2012,
       Y_2013
FROM (
    SELECT country_code AS country,
           AVG(funding_total) AS Y_2011
    FROM company
    WHERE EXTRACT(YEAR FROM founded_at::date) = '2011'
    GROUP BY country
) y_11
JOIN (
    SELECT country_code AS country,
           AVG(funding_total) AS Y_2012
    FROM company
    WHERE EXTRACT(YEAR FROM founded_at::date) = '2012'
    GROUP BY country
) y_12 ON y_11.country = y_12.country
JOIN (
    SELECT country_code AS country,
           AVG(funding_total) AS Y_2013
    FROM company
    WHERE EXTRACT(YEAR FROM founded_at::date) = '2013'
    GROUP BY country
) y_13 ON y_12.country = y_13.country
ORDER BY y_11.y_2011 DESC;
```

**Result:**
| country | y_2011      | y_2012      | y_2013     |
|---------|-------------|-------------|------------|
| PER     | 4e+06       | 41000       | 25000      |
| USA     | 2.24396e+06 | 1.20671e+06 | 1.09336e+06 |
| HKG     | 2.18078e+06 | 226227      | 0          |
| PHL     | 1.75e+06    | 4218.75     | 2500       |
| ARE     | 1.718e+06   | 197222      | 35333.3    |
| JPN     | 1.66431e+06 | 674720      | 50000      |
| AUT     | 1.5342e+06  | 147806      | 85773.3    |
| BRA     | 1.38007e+06 | 240639      | 67944.4    |
| DEU     | 1.1288e+06  | 1.32915e+06 | 66612.7    |
| ISR     | 1.03076e+06 | 1.27121e+06 | 294022     |
| PST     | 1e+06       | 0           | 0          |
| FRA     | 977874      | 291227      | 642083     |
| CHN     | 975918      | 611436      | 1e+06      |
| AUS     | 963088      | 192949      | 26313.7    |
| ...     | ...         | ...         | ...        |

**[⬆ Back to Top](#table-of-contents)**

---

## Project Conclusion

This PostgreSQL project demonstrates advanced proficiency in database querying and analysis through 23 progressive queries of increasing complexity. The work showcases essential data analyst competencies including:

**Core SQL Competencies:**
- **Complex joins and relationships**: Multi-table joins across 4+ tables (queries 12, 19), self-joins for acquisition analysis (query 20), and strategic use of LEFT/INNER joins
- **Advanced aggregation**: Multi-level GROUP BY operations, nested aggregations (queries 16-18), and aggregate functions across time dimensions
- **Subquery mastery**: Nested subqueries up to three levels deep (queries 13-16), correlated subqueries, and subqueries in WHERE/FROM/SELECT clauses
- **Window functions and CTEs**: Common Table Expressions for parallel data streams (query 22), creating pivot-style analyses through multiple JOINs (query 23)
- **Date/time manipulation**: EXTRACT functions for temporal filtering, year/month-based aggregations, and date range analysis across multiple years
- **Conditional logic**: CASE statements for dynamic categorization (queries 8-9), complex HAVING clauses, and multi-condition filtering
- **String pattern matching**: LIKE operations with multiple wildcard patterns, substring searches (queries 4-5)
- **Data formatting**: TO_CHAR for numeric display formatting, CONCAT for text manipulation, ROUND for presentation-ready metrics

**Analytical Thinking:**
- Translating business questions into efficient SQL logic
- Designing queries that balance performance with readability
- Building incremental complexity from simple filters to multi-CTE analyses
- Creating actionable insights from raw investment data

The project progresses logically from fundamental SELECT statements to sophisticated analyses involving Common Table Expressions, pivot-style reporting, and ROI calculations—demonstrating the systematic query design approach expected in customer experience analytics and business intelligence roles within the insurance sector.

**[⬆ Back to Top](#table-of-contents)**
