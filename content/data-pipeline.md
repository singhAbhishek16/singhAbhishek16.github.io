+++
title = "Data Pipeline"
+++

### 150TB/Day Security Data Pipeline

#### The Full Story

I inherited (& contributed ahead to development of) an Apache NiFi–based pipeline that moves security telemetry into SIEM — roughly 150TB/day, 
pulled from seven different source types (S3, SQS+S3, CloudWatch, Kinesis, Splunk TAs/UFs/HEC, Kafka, REST APIs) — and evolved it into the version 
running today: two active-active NiFi clusters backed by a ~5PB+ S3 data lake, with buffering & replay capabilities so the detection 
and incident response teams downstream never lose visibility, even when something breaks.

Here's what I built, why, and what I learned fixing issues when it didn't work.

#### Who this actually serves

This pipeline is the thing standing between "we have the data somewhere" and "the SOC can actually detect on it." SOC analysts, 
detection engineering, incident response, and legal/compliance all depend on it being right & on time. If it's late or wrong, 
that's a detection blind spot or a compliance gap — not just a missed SLA.

#### Building plugins that didn't exist

Coda, AWS Config, and MongoDB Ops Manager logs had no out-of-box NiFi processors. Rather than stitching together generic plugins &
scattering the ingestion logic across the flow, I built dedicated custom processors for each — keeping 
the ingestion logic for each source contained in one place instead of spread across a chain of generic components. Easier to 
reason about, easier to debug when something breaks.

#### Getting evidence back from deep archive

I built replay workflows that let Incident Response and Legal pull historical evidence out of deep-archive storage tiers and back 
into the SIEM.

#### When the SIEM went dark

At one point, the downstream SIEM stopped accepting data entirely. Nothing was lost — NiFi doesn't drop what it can't deliver — 
but everything started queuing up inside the pipeline itself, which isn't a sustainable place to hold terabytes of telemetry.

This is where Kinesis-based buffering strategy was designed: when direct path to the SIEM is unavailable, NiFi redirects 
the flow into Kinesis, where it sits with a 2-day retention window, and gets drained back into the pipeline and delivered once the 
SIEM is healthy again. When the SIEM actually went down, this is exactly what happened — data queued into Kinesis, sat there, and 
flowed back through cleanly once the SIEM recovered. No data loss, no manual intervention, no scramble to figure out where the 
backlog went.

#### The disk-fill bug that kept coming back

Separately, NiFi nodes started filling their disks & kept dropping out of the cluster — repeatedly. The first few times, the fix was 
the obvious one: bump the EBS volume size and move on. That got us out of the immediate incident, but it wasn't a fix, it was a 
delay — the same alert would fire again a few weeks later.

I went into NiFi's internals to understand how it actually manages disk space — specifically how it "claims back" space from stale 
data instead of just letting it accumulate. Once I understood that mechanism, I used it to design the right solution that 
reclaims space proactively, before nodes hit the wall. That alert hasn't needed a manual EBS bump since.

#### Cutting 30% of the payload — safely

Every event arriving through Kafka came wrapped in some metadata that had nothing to do with the actual event
content. This wrapper was pure overhead — but striping it out on a hunch had potential to break things downstream. I checked with the
downstream detection team first: were any of their detection rules keyed off that wrapper metadata? Once they confirmed no 
dependency, I built the filtering into the pipeline and payload got reduced by about 30%, which flowed directly into lower ingestion 
cost.

#### Splitting into two active-active clusters

The infra started as a single cluster. That meant every patch, every maintenance window, every node restart put live dataflow at
risk — if something went wrong mid-patch, telemetry stopped flowing to the SIEM.

It was redesigned to two active-active clusters, connected so either one can absorb the full dataflow while the other is down. 
This solved two problems at once: routine patching stopped being a risk event — one cluster gets safely patched while the other carries traffic,
then flip — and an unplanned node failure on one cluster no longer means a gap in detection coverage.