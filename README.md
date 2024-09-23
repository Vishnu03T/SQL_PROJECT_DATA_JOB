# Introduction
Dive into the data job market! Focusing on data analyst roles, this project explores & top-paying jobs,
in-demand skills, and where high demand meets high salary in data analytics.
• SQL queries? Check them out here: [project_sql] folder {/project_sql/}.
# Background
Driven by a quest to navigate the data analyst job market more effectively, this project was born from a desire to pinpoint top-paid and in-demand skills, streamlining others work to find optimal jobs.
Data hails from my [SQL Course] (https:/ Lukebarousse.com/sql). It's packed with insights on job titles, salaries, locations, and essential skills.
### The questions I wanted to answer through my queries:
1. What are the top-paying data analyst jobs?
2. What skills are required for these top_paying jobs?
3. What skills are most in demand for data analysts?
4. Which skills are associated with higher salaries?
5. What are the most optimal skills to develop?
# Tools I used
- SQL: The backbone of my analysis, allowing me to query the database and unearth critical insights.
- PostgreSQL: The chosen database management system, ideal for handling the job posting data.
- Visual Studio Code: My go-tg for database management and executing SQL queries.
- Git & GitHub: Essential for version control and sharing my SQL scripts and analysis, ensuring collaboration and project tracking.
# The Analysis
Each query for this project aimed at investigating specific aspects of the data analyst job market.
Here's how I approached each question:
### 1.Top paying Data Analyst jobs
To identify the highest-paying roles, I filtered data analyst positions by average yearly salary and location, focusing on remote jobs. This quen highlights the high paying opportunities in the field.
### Query

```sql
SELECT
    job_id,
    job_title,
    job_location,
    job_schedule_type,
    salary_hour_avg,
    job_posted_date,
    name AS company_name
FROM
    job_postings_fact
LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE 
    job_title_short = 'Data Analyst' AND 
    job_location = 'Anywhere' AND
    salary_year_avg IS NOT NULL
ORDER BY
    salary_year_avg DESC
LIMIT 10;
```
![Top_paying_jobs](https://raw.githubusercontent.com/Vishnu03T/SQL_PROJECT_DATA_JOB/main/query_1.jpg)
* Bar graph visualizing top paying jobs roels for Data Analyst

### 2.Skills for Top paying jobs
To understand what skills are required for the top-paying jobs, I joined the job postings with the skills data, providing insights into what employers value for high-compensation roles.
### Query
```sql
WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
    FROM
        job_postings_fact
    LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
    WHERE 
        job_title_short = 'Data Analyst' AND 
        job_location = 'Anywhere' AND
        salary_year_avg IS NOT NULL
    ORDER BY
        salary_year_avg DESC
    LIMIT 10
)

SELECT
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY 
    salary_year_avg DESC
```

![Top_Paying_Skills](https://raw.githubusercontent.com/Vishnu03T/SQL_PROJECT_DATA_JOB/main/query_2.jpg)

### Key Insights:
* SQL is the most in-demand skill, mentioned in 9 out of 9 job listings.
* Python is a close second, required in 8 out of 9 positions.
* Tableau is the most frequently mentioned visualization tool.
* Both traditional (Excel) and modern (Cloud platforms, Snowflake) tools are valued.
* R is still relevant, mentioned in nearly half of the listings.

### 3.In_Demand Skills for Data Analysts
```sql
SELECT
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM job_postings_fact
INNER JOIN skills_job_dim on job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' AND
    job_work_from_home = TRUE
Group BY
    skills
ORDER BY
    demand_count DESC
LIMIT 5
```
![In_demand_skills]()
