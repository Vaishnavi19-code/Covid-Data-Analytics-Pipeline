# COVID-19 Data Analysis & Automation Project

Project Overview:
This project focuses on building an end-to-end data pipeline to analyze COVID-19 data using SQL, Power BI, and n8n automation.
We processed multiple datasets (cases, vaccinations, and testing) to generate insights, build dashboards, and automate workflows for real-time decision-making.

Objectives:
- Build a complete ETL pipeline using SQL
- Clean and transform real-world COVID-19 datasets
- Create an interactive Power BI dashboard
- Automate workflows using n8n
- Generate actionable insights for decision-making

Datasets Used:
- COVID Cases Dataset
- Vaccination Dataset
- Testing Dataset

Project Workflow:
1. Extract
- Created SQL tables for all datasets
- Imported CSV data into database
- Verified data using row counts and sample records
- Checked for missing values and inconsistencies

2. Transform
- Standardized date formats (YYYY-MM-DD)
- Handled null values
- Created new features:
  - Daily New Cases
  - Total Confirmed Cases
  - Case Fatality Rate (CFR)
  - Joined datasets using state and date

3. Load
- Created final `covid_summary` table
- Calculated:
  - Positive Test Rate
  - Vaccination Rate

- Classified Risk Levels:
  - High Risk
  - Medium Risk
  - Low Risk

- Exported cleaned dataset for visualization

Power BI Dashboard:
Built an interactive dashboard to visualize:
- Vaccination progress
- State-wise comparison
- Trend analysis over time
- Risk level distribution

Key Insights:
- Variation in vaccination rates across states
- High-risk regions identified using CFR and positivity rate
- Growth trends in vaccination over time

n8n Automation:
- Designed workflows for automated processing
Workflow 1 - Covid-19 situation report

1. Schedule Trigger : Fires the workflow every 24 hours at 08:00 AM IST. No manual execution needed.
2. Read/Write Files from DiskFile SystemReads the covid_summary.xlsx file from the configured disk path as a binary buffer.
3. Extract from File (XLSX)TransformParses the binary XLSX buffer into an array of JSON objects. Each object = one state-date row with fields: State, Date, Confirmed, Deaths, Cured, etc.
4. Code in JavaScriptCodeIterates all rows to compute: national totals (confirmed, deaths, recovered), identifies the most affected state, calculates CFR and Recovery Rate, and builds the LLM prompt.
5. HTTP Request – Groq APIHTTPPOSTs the structured prompt to api.groq.com. Model: llama-3.3-70b-versatile. Returns a 3–4 paragraph professional situation report in natural language.
6. Send a Message – GmailGmailSends the AI-generated report + statistics footer to the configured recipient list. Subject auto-includes today's date.

workflow 2 - Covid Early warning system

1. Schedule Trigger : Fires every 6 hours for near-real-time monitoring. Configurable to any interval.
2. Read/Write Files from DiskFile SystemReads the latest version of covid_summary.xlsx. Assumes the file is updated regularly with new daily records.
3. Extract from File (XLSX)TransformConverts the XLSX binary to structured JSON rows for processing.
4. Code in JavaScriptCodeCore alert logic: sorts by date, identifies 3 most recent days, computes national aggregates, calculates 3-day moving average, evaluates against threshold, sets alert: true/false, builds prompt if alert triggered.
5. IF ConditionLogicRoutes workflow: alert === true → continues to HTTP + Gmail. alert === false → routes to No Operation.
6. (TRUE)HTTP Request – Groq APIHTTPSends the surge-specific prompt to Groq. Returns an AI-written alert brief with recommended public health actions.
7. (TRUE)Send Alert Email – GmailGmailDelivers the alert with an ALERT subject line, the AI-generated brief, and the 3-day trend statistics.6 (FALSE)No OperationTerminalSilent end node. Prevents unnecessary emails when no surge is detected.

Business Recommendations:
- Improve healthcare infrastructure in high-risk states
- Standardize data collection across regions
- Implement early warning systems
- Focus on low-performing regions for vaccination

Tools & Technologies:
- SQL (PostgreSQL)
- Power BI
- n8n
- Python (optional)
- Excel

Project Outcome:
- Built an end-to-end data pipeline
- Automated reporting workflows
- Improved data-driven decision-making

Future Improvements:
- Real-time data integration
- Advanced predictive modeling
- Deployment as a web dashboard

Team Contribution
This project was developed as a team effort:
- Data Engineering (ETL Pipeline)
- Data Analysis (SQL Queries)
- Dashboard Development (Power BI)
- Automation (n8n Workflows)
