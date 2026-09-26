+++
title = "Logsearch"
date = "2026-09-14T15:44:51+05:30"
+++

### LogSearch over archived data 

#### The Full Story

#### What problem does this solve
Friction in searching over years-old data & bill that comes with excessive ingestion to SIEM - reduces both. It allows investigators,
detection engineers, auditors to search on deep-archive information.

Saves cost by :
* eliminating need to ingest 5-years old data into SIEM for just taking a look if there could be something useful. This tool 
goes to the data and brings only the filtered result. 
* built on top of open-source projects - Apache Airflow and Dask

#### What can someone do now that they couldn't before
On-demand search with few clicks, without cross-team ticket approvals process. Massive search scale - 7PB of data searched in 24 hrs.  

#### What I owned
Development stage of LogSearh had authentication handled on EC2-hosted PostgreSQL. Database backups were manual & competed for resources
on the same machine as compute. Moving to production, application scaled to users from multiple teams. I planned a proactive move to 
AWS RDS. Led this migration in a scheduled maintenance window with zero downtime.

### How safe is this
* User-level control - User / Power User / Admin roles built into the app
* Infrastructure-level control - IAM roles & policies designed with least privilege idea; noone sees data outside their authorization. 

### What does this cost

