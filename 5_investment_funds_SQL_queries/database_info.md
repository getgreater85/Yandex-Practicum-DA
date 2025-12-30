In the project we will work with a database that stores information about venture funds and investments in startup companies. This database is based on the Startup Investments dataset published on the popular data mining competition platform Kaggle. 
Analyzing the investment market without preparation can be difficult. Therefore, first we will get acquainted with the important concepts that we will encounter when working with a database.

- **Venture funds** are financial organizations that can afford high risk and invest in companies with an innovative business idea or developed new technology, that is, in startups. The goal of venture funds is to receive significant profits in the future, which will be many times greater than the amount they spend on investing in the company. If the startup rises in price, the venture fund may receive a stake in the company or a fixed percentage of its revenue. 
- To make the financing process less risky, it is divided into stages - **rounds**. This or that round depends on what level of development the company has reached. 
- The first stages are **pre-seed** and **seed rounds**. A pre-seed round assumes that the company as such has not yet been created and is in the concept stage. The next seed round marks the growth of the project: the company’s founders are developing a business model and attracting investors. 
- If a company needs a mentor or coach, it attracts a business angel. **Business angels** - investors, who offer expert assistance in addition to financial support. This round is called an angel round. 
- When a startup becomes a company with a proven business model and begins to earn money on its own, there are more investor offers. This is round A, and others follow: B, C, D — at these stages the company is actively developing and preparing for an IPO. 
- Sometimes a **venture round** is allocated - financing that could come from a venture fund at any stage: initial or later.

In the investment data we will come across references to rounds, but the project does not assume that we should understand their specifics better than any investor. The main thing is to understand how the data is structured. 
We already know what an ER diagram is. It's best to start working with a new database by studying the schema.

![2023-01-10_00-09-14](https://user-images.githubusercontent.com/114194866/211475973-2aae351f-c53a-4109-87dd-a2753449bfdb.png)

Now we can get acquainted with the data that the tables store.

`acquisition` Contains information about purchases of some companies by others.
The table includes the following fields:
primary key `id` - identifier or unique purchase number;
foreign key `acquiring_company_id` - refers to the company table - the identifier of the purchasing company, that is, the one that it is buying another company;
foreign key `acquired_company_id` - refers to the table company - identifier of the company being purchased;
`term_code` — transaction payment method:
  
`cash` - in cash;
`stock` - company shares;
`cash_and_stock` - mixed payment type: cash and shares.
`price_amount` — purchase amount in dollars;
`acquired_at` — date of the transaction;
`created_at` — date and time when the record was created in the table;
`updated_at` — date and time the record in the table was updated.

`company` Contains information about startup companies.
primary key `id` - identifier, or unique company number;
`name` — company name;
`category_code` — category of the company’s activity, for example:
  
`news` - specializes in working with news;
`social` - specializes in social work.
`status` — company status:
  
`acquired` - acquired;
`operating` - operates;
`ipo` - entered into an IPO;
`closed` - ceased to exist.
`founded_at` — date of foundation of the company;
`closed_at` - the company's closing date, which is indicated if the company no longer exists;
`domain` — domain of the company website;
`twitter_username` — the name of the company’s Twitter profile;
`country_code` - country code, for example, USA for the USA, GBR for the UK;
`investment_rounds` — the number of rounds in which the company participated as an investor;
`funding_rounds` — the number of rounds in which the company attracted investments;
`funding_total` — the amount of attracted investments in dollars;
`milestones` — the number of important stages in the company’s history;
`created_at` — date and time when the record was created in the table;
`updated_at` — date and time the record in the table was updated.

`education` Stores information about the educational level of company employees.
primary key `id` - unique record number with information about education;
foreign key `person_id` - refers to the people table - the identifier of the person whose information is presented in the record;
`degree_type` — academic degree, for example:
`BA - Bachelor of Arts` - Bachelor of Arts;
`MS - Master of Science` - Master of Science.

`instituition` - educational institution, name of the university;
`graduated_at` — date of completion of training, graduation;
`created_at` — date and time when the record was created in the table;
`updated_at` — date and time the record in the table was updated.

`fund`
Stores information about venture funds. 
primary key `id` - unique number of the venture fund;
`name` — name of the venture fund;
`founded_at` — foundation date of the fund;
`domain` — domain of the fund’s website;
`twitter_username` — fund’s Twitter profile;
`country_code` — fund country code;
`investment_rounds` — number of investment rounds,
in which the fund took part;
`invested_companies` - the number of companies in which the fund invested;
milestones — the number of important stages in the fund’s history;
created_at — date and time of creation of a record in the table;
updated_at — date and time the record in the table was updated.
funding_round
Contains information about investment rounds.
primary key id - unique number of the investment round;
foreign key company_id - refers to the company table - the unique number of the company that participated in the investment round;
funded_at — date of the round;
funding_round_type — type of investment round, for example:
  
venture - venture round;
angel - angel round;
series_a — round A.
raised_amount — the amount of investment that the company raised in this round in dollars;
pre_money_valuation — preliminary assessment of the company’s value in dollars, carried out before investment;
participants — number of participants in the investment round;
is_first_round — whether this round is the first for the company;
is_last_round — whether this round is the last for the company;
created_at — date and time of creation of a record in the table;
updated_at — date and time the record in the table was updated.
investment
Contains information about investments of venture funds in startup companies.
primary key id - unique investment number;
foreign key funding_round_id - refers to the table funding_round - unique number of the investment round;
foreign key company_id - refers to the company table - the unique number of the startup company in which they invest;
foreign key fund_id - refers to the table fund - unique fund number,
investing in a startup company;
created_at — date and time of creation of a record in the table;
updated_at — date and time the record in the table was updated.
people
Contains information about employees of startup companies.
primary key id - unique employee number;
first_name — employee name;
last_name — employee’s last name;
foreign key company_id - refers to the company table - a unique number of the startup company;
twitter_username — employee’s Twitter profile;
created_at — date and time of creation of a record in the table;
updated_at — date and time the record in the table was updated.
