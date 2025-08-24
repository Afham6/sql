# Introduction
Dive into the data job market focusing on data analyst roles.this project explores top paying jobs,in demand skills and where high demand meet high salary in data analytics.

SQL queries? Check them out here:[project_sql folder](/project_sql/).
# Background
Driven by quest to navigate the data analyst job market more effectively,this project was born from a desire to pinpoint top paid and in demand skills,streamlining others work to find optimal jobs.

### The questions I wanted to answer through my SQL queries were:
1.What are the top paying data analyst jobs?

2.What skills are required for these top paying jobs?

3.What skills are most in demand for data analysts?

4.Which skills are associated with higher salaries?

5.What are the most optimal skills to learn?
# Tools I Used
For my deep dive into the data analyst job market, I harnessed the power of several key tools:

-**SQL:** The backbone of my analysis, allowing me to query the database and unearth critical insights.

-**PostgreSQL:** The chosen database management system, ideal for handling the job posting data.

-**Visual Studio Code:** My go-to for database management and executing SQL queries.

-**Git & GitHub:** Essential for version control and sharing my SQL scripts and analysis, ensuring collaboration and project tracking.
# The Analysis
### 1. Top Paying Data Analyst Jobs
The location focusing on india.This query highlights the high paying opportunities in the field.

```SQL
SELECT job_id,
        job_title,
        job_location,
        job_schedule_type,
        salary_year_avg,
        job_posted_date,
        name AS company_name
FROM
       job_postings_fact
LEFT JOIN company_dim on job_postings_fact.company_id = company_dim.company_id
WHERE
     job_title_short = 'Data Analyst' AND
     job_location = 'India' AND
     salary_year_avg IS NOT NULL
ORDER BY
         salary_year_avg DESC
LIMIT 10  
``` 
**Quick Summary:**

- High salaries (~90K–120K USD) cluster around senior or specialized analyst roles.

- Global companies (finance, healthcare, enterprise) are driving these top salaries.

- Clarivate is a standout repeat employer.

- Late 2023 postings dominate, showing strong demand at year-end.

![Top Paying Roles](project_sql\assets\top_paying_roles.png)  

### 2. Skills For Top Paying Job
To understand what skills are required for the top paying jobs. i joined the job posting with the skills data providing insights into what employers value for high compensation roles.
```SQL
WITH top_paying_jobs AS (
    SELECT
        job_id,
        job_title,
        salary_year_avg,
        name AS company_name
    FROM job_postings_fact
    LEFT JOIN company_dim 
        ON job_postings_fact.company_id = company_dim.company_id
    WHERE job_title_short = 'Data Analyst'
      AND job_location = 'India'
      AND salary_year_avg IS NOT NULL
    ORDER BY salary_year_avg DESC
    LIMIT 10
)

SELECT
    top_paying_jobs.*,
    skills
FROM top_paying_jobs
INNER JOIN skills_job_dim 
    ON top_paying_jobs.job_id = skills_job_dim.job_id
INNER JOIN skills_dim 
    ON skills_job_dim.skill_id = skills_dim.skill_id
ORDER BY salary_year_avg DESC;    
```
**Quick Summary:**
- SQL + Excel dominate → almost every top-paying job expects them.

- Python is critical — strong demand compared to R, highlighting Python’s dominance.

- Visualization & BI (Tableau) are valuable add-ons for analysts.

- A few roles expect ML/AI exposure (PyTorch, TensorFlow) — showing crossover with data science.

- Soft/project tools (Jira, Confluence) appear for senior roles, emphasizing collaboration.
![Skills for top paying job](project_sql\assets\top6_paying_data_analyst_skills.png)
### 3. In demand skills for data analyst
This query helped identify the skills most frequently requested in job posting and directing focus to areas with high demand.
```SQL
SELECT 
       skills,
       COUNT(skills_job_dim.job_id) AS demand_count
FROM
       job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
      job_title_short = 'Data Analyst' AND
      job_location = 'India' 
GROUP BY
       skills
ORDER BY
  demand_count  DESC
LIMIT 5 
```
**Quick Summary:**
- SQL dominates with 1,016 mentions, underlining its role as the foundational tool for querying and interacting with relational databases.

- Excel follows next with 717 mentions, highlighting its continued utility for exploratory analysis and report generation.

- Python, with 687 mentions, reflects the rising demand for scripting, automation, and performing more advanced data operations.

- Tableau (545 mentions) and Power BI (402 mentions) data visualization and dashboarding, essential for converting data into actionable business insights

| Skill    | Demand Count |
|----------|--------------|
| SQL      | 1016         |
| Excel    | 717          |
| Python   | 687          |
| Tableau  | 545          |
| Power BI | 402          |
Table of the demanded skills for data analyst in 202
### 4.Skills based salary
Exploring the average salaries associated with diffrent skills revealed which skills are the highest paying.
```SQL
SELECT 
       skills,
       round(avg(salary_year_avg),0) AS avg_salary
FROM
       job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
      job_title_short = 'Data Analyst' AND
      salary_year_avg IS NOT NULL AND
      job_location = 'India' 
GROUP BY
       skills
ORDER BY
  avg_salary DESC
LIMIT 25 
```
**Quick Summary:**

- SQL + Excel + Python are must-have baselines (high demand, decent pay).

- To unlock higher salaries, layer in:
Visualization (Power BI, Tableau)
Cloud skills (Azure)
Workflow/Business tools (Jira, Confluence, PowerPoint)

- Niche/enterprise tools (Visio, Flow, MS Access) can pay disproportionately well if you’re in industries like banking, healthcare, or large corporates.

- ML/AI frameworks (TensorFlow, PyTorch) give you cross-over potential into data science roles, often a pay bump over analyst roles.

| Rank | Skill       | Avg Salary (USD) |
|------|-------------|------------------|
| 1    | Visio       | 119,250          |
| 2    | Jira        | 119,250          |
| 3    | Confluence  | 119,250          |
| 4    | Power BI    | 118,140          |
| 5    | Azure       | 118,140          |
| 6    | PowerPoint  | 104,550          |
| 7    | Flow        | 96,604           |
| 8    | Sheets      | 93,600           |
| 9    | Word        | 89,579           |
| 10   | SQL         | 85,397           |
Table of the average salary for the top 10 paying skills for data analyst.
### 5.Most optimal skills to learn
Combining insights from demand and salary data this query aimed to pinpoint skills that are both in high demand and have high salaries and this offers a strategic focus for skill development.
```SQL
SELECT
       skills_dim.skill_id,
       skills_dim.skills,
       count(skills_job_dim.job_id) as demand_count,
       round(avg(job_postings_fact.salary_year_avg),0) AS avg_salary
FROM
     job_postings_fact
INNER JOIN skills_job_dim ON job_postings_fact.job_id = skills_job_dim.job_id
INNER JOIN skills_dim ON skills_job_dim.skill_id = skills_dim.skill_id
WHERE
      job_title_short = 'Data Analyst' AND
       salary_year_avg IS NOT NULL AND
    job_work_from_home = TRUE
GROUP BY
       skills_dim.skill_id
HAVING
       count(skills_job_dim.job_id) > 10       
ORDER BY
        avg_salary DESC,
        demand_count DESC
LIMIT 25;
```
**Quick Summary:**

- SQL + Excel + Python are must-have baselines (high demand, decent pay).

- To unlock higher salaries, layer in:
Visualization (Power BI, Tableau)
Cloud skills (Azure)
Workflow/Business tools (Jira, Confluence, PowerPoint)

- Niche/enterprise tools (Visio, Flow, MS Access) can pay disproportionately well if you’re in industries like banking, healthcare, or large corporates.

- ML/AI frameworks (TensorFlow, PyTorch) give you cross-over potential into data science roles, often a pay bump over analyst roles.

| Rank | Skill       | Demand Count | Avg Salary (USD) |
|------|------------|--------------|-----------------|
| 1    | Python     | 236          | 101,397         |
| 2    | Tableau    | 230          | 99,288          |
| 3    | R          | 148          | 100,499         |
| 4    | SAS        | 63           | 98,902          |
| 5    | Looker     | 49           | 103,795         |
| 6    | Snowflake  | 37           | 112,948         |
| 7    | Oracle     | 37           | 104,534         |
| 8    | Azure      | 34           | 111,225         |
| 9    | AWS        | 32           | 108,317         |
| 10   | SQL Server | 35           | 97,786          |
Table of the most optimal skills for data analyst sorted by salary.

# What I Learned
- **SQL for Data Analysis**

How to query large datasets, join multiple tables, filter data, aggregate metrics, and handle NULL/missing values.

Learned to write complex queries to answer real business questions like top-paying jobs, most in-demand skills, and salary analysis.

- **Data Cleaning & Transformation**

Identified duplicates, structured raw job postings data, and normalized skill sets for analysis.

Prepared datasets for accurate aggregation and reporting.

- **Data Aggregation & Insights Generation**

Calculated average salaries by job title, skill, and company.

Ranked skills and jobs based on pay and demand.

Interpreted results to generate actionable insights for career trends in 2023.

- **Analytical Thinking & Reporting**

Learned how to structure analysis to answer business questions.

Developed the ability to combine salary data with demand metrics to identify optimal skills for career growth.

Practiced summarizing technical findings into clear insights suitable for decision-making.

- **Business Awareness**

Gained an understanding of which skills and roles are valued in the data analytics job market.

Learned to differentiate between high-demand skills and high-paying niche skills.
# Conclusion
### Insights
From the analysis several general insights emerged:
1. **Top paying data analyst jobs**:

 High salaries (~90K–120K USD) cluster around senior or specialized analyst roles.

 Global companies (finance, healthcare, enterprise) are driving these top salaries.

 Clarivate is a standout repeat employer.

Late 2023 postings dominate, showing strong demand at year-end.
2. **Skills for top paying jobs**:

SQL + Excel dominate → almost every top-paying job expects them.

 Python is critical — strong demand compared to R, highlighting Python’s dominance.

 Visualization & BI (Tableau) are valuable add-ons for analysts.

 A few roles expect ML/AI exposure (PyTorch, TensorFlow) — showing crossover with data science.

 Soft/project tools (Jira, Confluence) appear for senior roles, emphasizing collaboration.

 3. **Most in demand skills**:

 SQL dominates with 1,016 mentions, underlining its role as the foundational tool for querying and interacting with relational databases.

 Excel follows next with 717 mentions, highlighting its continued utility for exploratory analysis and report generation.

 Python, with 687 mentions, reflects the rising demand for scripting, automation, and performing more advanced data operations.

 Tableau (545 mentions) and Power BI (402 mentions) data visualization and dashboarding, essential for converting data into actionable business insights

 4. **skills with higher salaries**:

 SQL + Excel + Python are must-have baselines (high demand, decent pay).

To unlock higher salaries, layer in:
Visualization (Power BI, Tableau)
Cloud skills (Azure)
Workflow/Business tools (Jira, Confluence, PowerPoint)

Niche/enterprise tools (Visio, Flow, MS Access) can pay disproportionately well if you’re in industries like banking, healthcare, or large corporates.

ML/AI frameworks (TensorFlow, PyTorch) give you cross-over potential into data science roles, often a pay bump over analyst roles.

5. **Optimal skills for job market value**:

SQL + Excel + Python are must-have baselines (high demand, decent pay).

To unlock higher salaries, layer in:
Visualization (Power BI, Tableau)
Cloud skills (Azure)
Workflow/Business tools (Jira, Confluence, PowerPoint)

Niche/enterprise tools (Visio, Flow, MS Access) can pay disproportionately well if you’re in industries like banking, healthcare, or large corporates.

ML/AI frameworks (TensorFlow, PyTorch) give you cross-over potential into data science roles, often a pay bump over analyst roles.
### Closing thoughts
This project enhanced my SQL skilled and provided valuable insights into the data analyst job market. the findings from the analysis serve as a guide to prioritizing skill development and job search efforts.apsiring data analyst can better position themselves in a competitive job market by focusing on high demand,high salary skills.this exploration highlights the importance of continuous learning and adaptation to emerging trend in the field of data analytics.




