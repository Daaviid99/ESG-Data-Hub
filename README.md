# ESG-Data-Hub
In this project, I defined the Space where ESG Data lives. Thanks to the ETL process, which feeds the data into a DuckDB database with changes tracked with dbt and some log monitoring (chatbot conn), the Data Lineage is tracked with dbt too. All of this information connects to our Open metadata hosted locally thanks to Docker. Final products: Power BI & Excel

Tired of reporting the same information, I have created an ESG Data Hub or ESG Dataverse for our corporation, where I can search whenever I need any ESG KPIs for reporting. Data products that come from this app are two:

Dashboard in Power BI that visualises our ESG Dataverse.
Excel files that feed the Dashboard mentioned before.

With some extra layers that enhance the solution:

Auditability is provided by dbt and logs monitoring.
Data Governance: Every data system needs a Data governance system, so I trusted Open Metadata, hosted locally thanks to Docker.

This solution could be amazing for me, but for someone not a specialist, this information is just numbers. For this reason, 2 specialised agents are connected to a database: the Analyst Agent that queries all the data we need in SQL, and the ESG Specialist, which receives the query from the first one and interprets it to create a well-designed Executive Report.

2. How does it start?

Temperatures are hitting up to 30ºC in Andalucia, and the days are starting to take a little bit more effort, but open the mail app and you have received another email. And another stakeholder is claiming a new report or information.

Another one.

This year started with the Sustainability Report, which was followed by TOP Employer certification and our Investor Due Dilligence report. After this journey, our corporation updates its Greenhouse Emissions Inventory (GEI).

I would have started to hate data if it hadn't been the purpose that made me work as an ESG Specialist, so smiling and let's do it.

This is my day-to-day, but it doesn't seem the most efficient way, doesn't it?

After analysing all the KPIs deeply, I was reporting in each Report, 80% of those it was the same; in other words, the 80% of KPIs I was reporting were represented in every report.

Why not define these KPIs as our key metrics? Moreover, if we go deeper, defining them as our key metrics, why not define a solid and proper architecture to extract-transform and load?

This is how our ESG Datahub was born.

Why Existing Sustainability Reporting Technology Didn't Work for Me

For sure, I did. But the result didn't convince me:

High prices: with pricing models tied to data volume or user seats.
Low flexibility: the software dictates how you track and report data, not the other way around.
No data ownership: your data doesn't belong to you, belongs to your software provider.
Limited cross-dataset analysis: most SaaS tools align with standard reporting frameworks, but combining operational data with ESG data to surface new insights is rarely supported.

Most critically, you build a dependency on an external provider, and that was a dependency I wasn't willing to accept.

Reasons I wasn't willing to accept. I mean, these kinds of resources are great, but the feel of being digitally mature encourages me to delve more into the technological stack.

So I defined that I wanted to go further into this topic, so I analysed my digital toolkit: good analytics skills, good Excel skills, good enough Power BI skills, good in the Data Science field, and starting to explore Data Engineer tools.

This toolkit seems to be enough to learn a lot and resolve my problem. For sure, it will be an enjoyable journey and quite educational for me.

3. Building an ESG Data Automation System: Architecture and Design Decisions

My real goal was to reduce reporting hours by extracting the same KPIs without relying on AI, due to concerns around Data Auditability. Audits require traceability, not the black-box approach and hallucinations that AI tends to produce.

1. Defining Database

The first step was quite easy: transforming Excel Data into a real DuckDB Database to receive the benefits that DB systems produce. The data flows between different stages of our ETL (extract-transform-load) process, but the admin must be informed about every failed execution or successful one. This problem is easy to solve thanks to the existence of log monitoring, where the admin can track every execution of the whole pipeline.

That sounds nice as a perfect system, but how does the data get ingested into this system? Here is where I ned some scheduling via cron, which is how we inform our computer about "human time". The cron job triggers on the 15th of each month and runs a two-phase reconciliation process:

Extraction & insertion: detects new invoices not yet present in the database, extracts their data and inserts new records.
Reconciliation/update: compares already-existing invoice records in the database against the source, and updates any records where discrepancies are found.
2. Audibility

All of these changes are registered by our system of audability without altering our Data lineage. This is where dbt emerged from the shadows as the essential part of the project. Two main functions are covered by dbt:

dbt defines data lineage (which means all the transformations our data suffered through our ETL), thanks 2 main functionalities: source which is a function that refers to the original raw tables defined by sources.yml file, and ref which references other dbt models already transformed, allowing dbt to build the full transformation chain automatically.
Thanks to dbt snapshot Every data change is monitored by dbt , even if a change has overwritten a value, dbt will monitor "the story of this value" by keeping all its previous versions with a validity period.

3. Agents

Nice approach, we could argue, but the first problem emerged: if all our ESG Data is stored in our database, that means someone should have known how to query data with SQL.

The solution is probably not the most efficient way, but the fastest one: create a chatbot distributed by 3 agents:

A data analyst who is responsible for creating SQL queries to extract data from our database. Quite important: the instructions define that this agent is not able to delete or insert data


An ESG Specialist who receives the data from our first agent and analyses it with ESG vision and critical thinking. The output from the agent is divided into 5 main tasks:
Generates an Executive Summary with the most relevant ESG insights from the data.
Performs anomaly detection, flagging unusual patterns or values that may require attention.
Maps the results against GRI and other ESG frameworks, with the possibility of connecting it to a RAG system fed with the official framework guidelines.
Extracts the most significant highlights, making key findings immediately visible.
Provides a full interpretation of the results, explaining what the numbers actually mean for the organisation.
ESG Designer: querying and interpreting data solves half of the problem. The other half is that Direction doesn't read SQL outputs; they read slides and reports. Our ESG Designer Agent takes the interpreted output and automatically generates a ready-to-present report adapted to our monthly reporting format, turning hours of manual formatting into just another step in the pipeline.
The final product is a nice picture of our question: summary, problems or anomalies and the implications of those data.

This Data Analytics tool acts as an independent consultant for our Direction or other stakeholders, but this is not our main Business Intelligence tool.

4. Final data products

Our final product is not just another chatbot; our latest release delivers tangible, conventional outputs: final products are Excel files, which are the data language of most companies, with one file dedicated to each table. These Excel files feed into a Dashboard that visualises all our ESG data with date filters applied.

Every ETL process overwrites the Excel files with the latest changes, so any modifications will be reflected in the files, and Power BI will display them once you click "Refresh".

5. ESG Data Hub UI

Having all our data stored and transformed is great, but a new question appears: who can access what? And more importantly, can I trust what I'm seeing?

This is where OpenMetadata enters the picture, hosted locally via Docker, again, keeping full data ownership. OpenMetadata solves three concrete problems:

I don't know what this column means: every table, field and transformation is documented and discoverable. Context lives next to the data.
I can't trace where this number comes from: the full journey of our data is visualised, from raw source files to the final Excel outputs and Dashboard, keeping our auditability system complete end-to-end.
Everyone has access to everything: admins define who sees what. Not every stakeholder needs access to every dataset, and OpenMetadata enforces that boundary.
3. The Result: A Complete System for Digitalising Sustainability Reporting

What started as a personal frustration ended up as something that actually works:

✅ A scheduled ETL that ingests, reconciles and transforms ESG data monthly
✅ Full auditability via dbt snapshots and execution logs — audit-ready without black-box AI
✅ Two core data products: Power BI Dashboard + structured Excel files
✅ An AI agent layer for on-demand ESG interpretation and executive reporting
✅ Data governance via OpenMetadata with role-based access control
