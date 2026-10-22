# Introduction
This project focuses on the data science job market, with a special focus on the data anlayst role. The project explores top-paying roles, in-demand skills and whether or not those skills are associated with higher salaries.

Check out the sql queries here: [SQL_Project](/SQL_Project/)

# Background
I wanted to navigate the data analytics job market as I am actively trying to improve my skills in this field. This project pinpoints the highest paid and most in-demand skills by anlaysing job postings throughout the world.

Data analysis was conducted after creating a local database on postgres sql using data from [Data](https://lukebarousse.com/sql). The data mainly focussed on job titles, locations, salaries and essential skills.

# Questions answered in the exploratory data analysis:
1. What are the top-paying data analyst jobs?
2. What skills are required for these high paying jobs?
3. What are the most in-demand skills for data analysts?
4. Which skills are associated with higher salaries?
5. What are the most optimal skills to learn as a data analyst?

# Tools I used
- SQL (PostgresSQL)
- Visual Studio Code
- Git and GitHub

# The Analysis
Every query had the goal of investigating specific aspects of the data analytics job market:

### Top Paying Jobs
I identified the top 10 data analyst roles based on the yearly salary with a focus on remote positions.
```sql
SELECT 
  job_id,
  job_title,
  job_location,
  job_schedule_type,
  salary_year_avg,
  job_posted_date,
  name AS company_name
FROM
  job_postings_fact
  LEFT JOIN company_dim ON job_postings_fact.company_id = company_dim.company_id
WHERE
  job_title_short = 'Data Analyst' AND job_location = 'Anywhere'
  AND salary_year_avg IS NOT NULL
ORDER BY salary_year_avg DESC
LIMIT 10;
``` 
Heres a breakdown:
- Top data analyst salaries for remote positions range from $184000 to $650000 indicating great salary potential.
- Organisations such Meta, AT&T and Pinterest seem to offer high salaries.
- There is a high divesity of job titles such Director of Analytics or Marketing Data Analyst.

### Top Paying Job Skills
I have identified the core skills required for top-paying data analyst roles.
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
  job_title_short = 'Data Analyst' AND job_location = 'Anywhere'
  AND salary_year_avg IS NOT NULL
ORDER BY 
    salary_year_avg DESC
LIMIT 10)

SELECT
    top_paying_jobs.*,
    skills
FROM
    top_paying_jobs
    INNER JOIN skills_job_dim ON top_paying_jobs.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY
    salary_year_avg DESC;
```
Heres a breakdown:
- SQL, python and R are the most important tools for data analysts.
- Business intelligence and spreadsheet tools such Power BI and Excel are also included.

### Top Demanded Skills
I have identified the top 5 most in-demand skills for all data analyst job postings.
```sql
SELECT
    skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM
   job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst'
GROUP BY skills
ORDER BY demand_count DESC
LIMIT 5;
```
Heres a breakdown:
- The most in-demand skills include SQL, Excel, Python, Tableau and Power BI.
- This indicates that even though there are many skills out there, the above tools remain the go to for data analysis and business intelligence.

### Top Paying Skills
I looked at the average salaries associated with each skill, regardless of job location.
```sql
SELECT
    skills,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM
   job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' AND salary_year_avg IS NOT NULL
GROUP BY skills
ORDER BY avg_salary DESC
LIMIT 30;
```
Heres a breakdown:
- Higher salaries are associated with big data and ML skills along with software development and cloud computing skills.
- This shows that the role of a data analyst is slowly evolving into a more technical role requiring a bigger skillset.

### Optimal Skills
I looked at the most optimal skills for a data analyst. These are skills that are not just financially rewarding, but also offer high job security.
```sql
WITH skills_demand AS (
SELECT
    skills_dim.skill_id,
    skills_dim.skills,
    COUNT(skills_job_dim.job_id) AS demand_count
FROM
   job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' AND salary_year_avg IS NOT NULL
    AND job_work_from_home = TRUE 
GROUP BY skills_dim.skill_id),
    
average_salary AS (
SELECT
    skills_job_dim.skill_id,
    ROUND(AVG(salary_year_avg), 0) AS avg_salary
FROM
    job_postings_fact
    INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
    INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
    job_title_short = 'Data Analyst' AND salary_year_avg IS NOT NULL
    AND job_work_from_home = TRUE 
GROUP BY skills_job_dim.skill_id)


SELECT
    skills_demand.skill_id,
    skills_demand.skills,
    demand_count,
    avg_salary
FROM
    skills_demand
    INNER JOIN average_salary ON skills_demand.skill_id = average_salary.skill_id
WHERE demand_count > 10
ORDER BY avg_salary DESC
LIMIT 25;
```
Heres a breakdown:
- There is high-demand for programming languages like python or R, with a demand count of 236 and 148 respectively.
- Cloud tools like Azure also show high demand.
- Tableau seems to be the most optimal business intelligence tool.
- SQL is still in demand, indicating that there will always be a need data storage, retrieval and manipulation.

# What I Learned
- Complex queries: I have significantly improved my skills when using CTEs in combinantion with multiple joins.
- Analytical thinking: I improved my real-world thinking skills, turning questions into actionable and insightful sql queries.

# Conclusion
This project enhanced my sql skills and provided valuable insights into the job market. This analysis serves as a guide to prioritize skill development and job search efforts. The importance of improving your skills is also highligted as the data anlayst position continues to evolve.
