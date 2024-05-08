# Data-Engineer---Data-Camp
- Data Engineer Associate Practical Exam
- Mental Health Project

# Practical Exam: Loan Insights

EasyLoan offers a wide range of loan services, including personal loans, car loans, and mortgages.

EasyLoan offers loans to clients from Canada, United Kingdom and United States.

The analytics team wants to report performance across different geographic areas. They aim to identify areas of strength and weakness for the business strategy team.

They need your help to ensure the data is accessible and reliable before they start reporting.


**Database Schema**

The data you need is in the database named `lending`.

<img width="782" alt="image" src="https://github.com/MiguelAngelHorta/Data-Engineer---Data-Camp/assets/106134627/43eb2260-ff83-4a92-a6e6-5b6588603812">


# Task 1 

The analytics team wants to use the `client` table to create a dashboard for client details. For them to proceed, they need to be sure the data is clean enough to use.

The `client` table below illustrates what the analytics team expects the data types and format to be.

Write a query that makes the `client` table match the description provided. Your query should not update the `client` table.

| Column Name       | Description                                                      |
|-------------------|------------------------------------------------------------------|
| client_id         | Unique integer (set by the database, can’t take any other value) |
| date\_of\_birth       | Date of birth of the client, as a date  (format: YYYY-MM-DD)                              |
| employment_status        | Current employment status of the client, either employed or unemployed, as a lower case string                              |
| country          | The country where the client resides, either USA, UK or CA, as an upper case string                      |

``` SQL
SELECT 
    client_id,
    SUBSTRING(TO_CHAR(date_of_birth::DATE, 'YYYY-MM- DD'), 1, 11) AS date_of_birth,
    CASE
        WHEN LOWER(employment_status) LIKE 'em%' THEN 'employed'
        WHEN LOWER(employment_status) LIKE 'un%' THEN 'unemployed'
        WHEN LOWER(employment_status) IN ('part-time', 'full-time') THEN 'employed'
        ELSE employment_status
    END AS employment_status,
    UPPER(country) AS country
FROM 
    client
```

<img width="809" alt="image" src="https://github.com/MiguelAngelHorta/Data-Engineer---Data-Camp/assets/106134627/985db004-5e02-447c-bbe0-e1fa6beb86fc">

# Task 2

You have been told that there was a problem in the backend system as some of the `repayment_channel` values are missing. 

The missing values are critical to the analysis so they need to be filled in before proceeding.

Luckily, they have discovered a pattern in the missing values:

- Repayment higher than 4000 dollars should be made via `bank account`.
- Repayment lower than 1000 dollars should be made via `mail`.

Write a query that makes the `repayment` table match this criteria.

``` SQL

SELECT 
    repayment_id, 
    loan_id,
    repayment_date,
    repayment_amount,
    CASE 
        WHEN repayment_channel = '-' THEN
            CASE 
                WHEN repayment_amount > 4000 THEN 'bank account'
                WHEN repayment_amount < 1000 THEN 'mail'
                ELSE repayment_channel
            END
        ELSE repayment_channel
    END AS repayment_channel
FROM repayment;
```
<img width="830" alt="image" src="https://github.com/MiguelAngelHorta/Data-Engineer---Data-Camp/assets/106134627/8d784f98-256e-42a1-8164-7bf3165194af">

# Task 3

Starting on January 1st, 2022, all US clients started to use an online system to sign contracts.

The analytics team wants to analyze the loans for US clients who used the new online system.

Write a query that returns the data for the analytics team. Your output should include `client_id`,`contract_date`, `principal_amount` and `loan_type` columns.

<img width="574" alt="image" src="https://github.com/MiguelAngelHorta/Data-Engineer---Data-Camp/assets/106134627/c23ec116-21e5-4014-870e-40e279416dfb">


``` SQL
SELECT lo.client_id,
       con.contract_date,
       lo.principal_amount,
       lo.loan_type
FROM loan lo
JOIN contract con ON lo.contract_id = con.contract_id
JOIN client cli ON lo.client_id = cli.client_id
WHERE con.contract_date >= '2022-01-01' AND cli.country = 'USA';
```
<img width="806" alt="image" src="https://github.com/MiguelAngelHorta/Data-Engineer---Data-Camp/assets/106134627/6b2b2327-aad8-4ad2-8061-874feb3c051f">


# Task 4

The business strategy team is considering offering a more competitive rate to the US market. 

The analytic team want to compare the average interest rates offered by the company for the same loan type in different countries to determine if there are significant differences.

Write a query that returns the data for the analytics team. Your output should include `loan_type`, `country` and `avg_rate` columns.
<img width="572" alt="image" src="https://github.com/MiguelAngelHorta/Data-Engineer---Data-Camp/assets/106134627/91777476-f9de-4f01-97ec-976bee1782da">



``` SQL
SELECT lo.loan_type,
       cli.country,
       AVG(lo.interest_rate) AS avg_rate
FROM loan lo
JOIN client cli ON lo.client_id = cli.client_id
GROUP BY lo.loan_type, cli.country;
```
<img width="808" alt="image" src="https://github.com/MiguelAngelHorta/Data-Engineer---Data-Camp/assets/106134627/9e4a547e-b497-47d3-9cf2-d32032f84267">





## Mental Health Project
Explore and analyze the students data to see how the length of stay (stay) impacts the average mental health diagnostic scores of the international students present in the study.

Return a table with nine rows and five columns.
The five columns should be aliased as: stay, count_int, average_phq, average_scs, and average_as, in that order.
The average columns should contain the average of the todep (PHQ-9 test), tosc (SCS test), and toas (ASISS test) columns for each length of stay, rounded to two decimal places.
The count_int column should be the number of international students for each length of stay.
Sort the results by the length of stay in descending order.


| Field Name    | Description                                             |
|---------------|---------------------------------------------------------|
| inter_dom     | Types of students (international or domestic)          |
| japanese_cate | Japanese language proficiency                           |
| english_cate  | English language proficiency                            |
| academic      | Current academic level (undergraduate or graduate)      |
| age           | Current age of student                                 |
| stay          | Current length of stay in years                        |
| todep         | Total score of depression (PHQ-9 test)                  |
| tosc          | Total score of social connectedness (SCS test)          |
| toas          | Total score of acculturative stress (ASISS test)        |

``` SQL
SELECT stay, 
       COUNT(*) AS count_int,
       ROUND(AVG(todep), 2) AS average_phq, 
       ROUND(AVG(tosc), 2) AS average_scs, 
       ROUND(AVG(toas), 2) AS average_as
FROM students
WHERE inter_dom = 'Inter'
GROUP BY stay
ORDER BY stay DESC;
```
