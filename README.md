# Introduction
Dive into the data job market! Focusing on data analyst roles, this project explores & top-paying jobs,
in-demand skills, and where high demand meets high salary in data analytics.
# Background
Driven by a quest to navigate the data analyst job market more effectively, this project was born from a desire to pinpoint top-paid and in-demand skills, streamlining others work to find optimal jobs.
Data hails from my [SQL Course](https:/Lukebarousse.com/sql). It's packed with insights on job titles, salaries, locations, and essential skills.
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
![In_demand_skills](https://raw.githubusercontent.com/Vishnu03T/SQL_PROJECT_DATA_JOB/main/query_3.jpg)
### Key Insights
* SQL is by far the most in-demand skill, mentioned in 7,291 job listings.
* Excel remains highly relevant, being the second most in-demand skill.
* Python is close behind Excel, highlighting its importance in modern data analysis.
* Tableau and Power BI are the most sought-after data visualization tools.
* The demand for SQL is almost 3 times higher than for Power BI, the least demanded skill in the top 5.

### 4.Skills based on salary
exploaring the average salaries associated with different skills revealed which skills are the highest paying.
### Query
```sql
SELECT
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim on job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' AND
    salary_year_avg IS NOT NULL
    --job_work_from_home = TRUE
Group BY
    skills
ORDER BY
    avg_salary DESC
LIMIT 25
```
![Salary_based](https://raw.githubusercontent.com/Vishnu03T/SQL_PROJECT_DATA_JOB/main/query_4.jpg)
### Key Insights
* SVN (Subversion) tops the list with a significantly higher average salary.
* Blockchain technology (Solidity) is the second highest-paying skill.
* Database skills (Couchbase) and AI/ML platforms (DataRobot) are highly valued.
* Golang, a modern programming language, rounds out the top 5.
* There's a substantial gap between the top skill (SVN) and the others in terms of average salary.

### 5.Most optimal skill to learn
Combining insights from demand and salary data, this query aimed to pinpoint skills that are both in high demand and high salaries, offering a strategic focus for skill development.
### Query
```sql
SELECT
    skills_dim.skill_id,
    skills_dim.skills,
    COUNT(skills_job_dim.job_id) AS demand_count,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM job_postings_fact
INNER JOIN skills_job_dim on job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' AND
    salary_year_avg IS NOT NULL
    AND job_work_from_home = TRUE
GROUP BY 
    skills_dim.skill_id
HAVING
    count(skills_job_dim.job_id)>10
ORDER BY
    avg_salary DESC,
    demand_count DESC
limit 25;
```
![Optimal_skill_to_learn](https://raw.githubusercontent.com/Vishnu03T/SQL_PROJECT_DATA_JOB/main/query_5.jpg)
### Key Insights
* Go, Confluence, and Hadoop offer the highest average salaries, exceeding $110,000.
* Cloud technologies like Snowflake, Azure, and AWS are well-compensated, reflecting their importance in the data ecosystem.
* Traditional tools like SQL Server, SAS, and JavaScript still command respectable salaries.
* Python and R, the two most popular programming languages for data analysis, are ranked 13th and 14th respectively by average salary.
* Tableau, a leading data visualization tool, is ranked 17th, suggesting its value extends beyond just high salaries.

# What I learned
Throughout this adventure, I've turbocharged my SQL toolkit with some serious firepower:
-* Complex Query Crafting:** Mastered the art of advanced SQL, merging tables like a pro and wielding WITH clauses for ninja-level temp table maneuvers.
- ** Data Aggregation:** Got cozy with GROUP BY and turned aggregate functions like COUNT() and AVG () into my data-summarizing sidekicks. I
- ** Analytical Wizardry:** Leveled up my real-world puzzle-solving skills, turning questions into actionable, insightful SOL queries.
# Conlusions
### Insights
1. **Top-Paying Data Analyst Jobs**: The highest-paying jobs for data analysts that allow remote work offer a wide range of salaries, the highest at $650,000!
2. **Skills for Top-Paying Jobs**: High-paying data analyst jobs require advanced proficiency in
SQL, suggesting it's a critical skill for earning a top salary.
3. **Most In-Demand Skills**: SQL is also the most demanded skill in the data analyst joll market, thus making it essential for job seekers.
4. **Skills with Higher Salaries**: Specialized skills, such as SV and Solidity, are associated with the highest average salaries, indicating a premium on niche expertise.
5. **Optimal Skills for Job Market Value**: SQL leads in demand and offers for a high average salary, positioning it as one of the most optimal skills for data analysts to learn to maximize their market value.
### Closing Thoughts
This project enhanced my SQL skills and provided valuable insights into the data analyst job market. The findings from the analysis serve as a guide to prioritizing skill development and job search efforts. Aspiring data analysts can better position themselves in a competitive job market by focusing on high-demand, high-salary skills. This exploration highlights the importance of continuous learning and adaptation to emerging trends in the field of data analytics.
