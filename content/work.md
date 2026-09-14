+++
title = "Work"
menu = "main"
+++

#### At a glance

### 150TB/Day Security Data Pipeline

I design & own pipelines that get ~150TB of security data into SIEM every day, across ~7 source types, backed with a 5PB+ datalake - designed to survive failures.
Pipeline infra is designed with buffer strategy to handle situation when downstream SIEM could go dark. I have developed custom plugins to support specific requirements;
fixed bugs to keep the solution running. Here's how it's built, what broke, and what I did about it. [————→](https://abhishek-singh.dev/data-pipeline)

### LogSearch over archived data 

Built to search on logs stored in S3 & Dynamodb, eliminating need to ingest to SIEM. This expands search to deep-archive storage, sometimes
as old as 5 years. Used by cross-cybersecurity teams for running investigations & audits. It has been able to support searches over 7PB
in 24 hrs window. This has been built by the awesome team I got to work with. Here are sections I owned, and what learned working on them.
[————→](https://abhishek-singh.dev/logsearch)

### Honeypot Zoom meetings

Built to tackle Zoom-bombing problem during covid-19. It's a solution to launch & manage honeypot meetings to collect habitual bad actors' data.
End-to-end automated, from meeting launches to data collection. Used by Trust & Safety team. Org priorities shifted, and this project could not see light of production.
But here is what I learned building this from ground up. [————→](https://abhishek-singh.dev/honeypots)