# Capstone Project: Production-Ready Event Analytics Pipeline

This capstone was about building a production-style analytics pipeline for high-volume e-commerce event data on AWS. A provided CloudFormation template generates 500K–750K user events every five minutes into S3; I extended that infrastructure with my own pipeline components and delivered working queries plus a portfolio-ready blog post.

For the full write-up, see the capstone blog post in this repository.

## Project Overview

The scenario: I am a data engineer at an e-commerce company. The product analytics team has raw event logs in S3, but data scientists cannot easily query them. My job is to turn those raw JSON logs into a reliable, performant analytical dataset that supports concrete business questions.

Core goals:

- Ingest and structure high-volume JSON event logs in S3.
- Process new data incrementally (every five minutes) without reprocessing everything.
- Produce a clean, typed Parquet dataset partitioned by time.
- Register the dataset in the Glue Data Catalog and query it from Athena.
- Support five specific analytics use cases around funnels, revenue, product popularity, categories, and user activity.

## Architecture

- **CloudFormation template**
  - Provisions the base infrastructure and event generator.
- **Lambda + EventBridge**
  - Runs every 5 minutes.
  - Writes one gzipped JSON Lines file per run to S3.
  - Uses Hive-style partitioning:
    - `events/year=YYYY/month=MM/day=DD/hour=HH/minute=mm/events-{timestamp}.jsonl.gz`
- **S3**
  - Raw events lake (`events/`).
  - Curated, partitioned Parquet dataset for analytics.
- **AWS Glue (Spark)**
  - ETL job to read raw JSON events, enforce schema, and write Parquet.
  - Incremental processing using Glue job bookmarks / partition-based logic.
  - Registers curated tables in the Glue Data Catalog.
- **Amazon Athena**
  - SQL access to the curated dataset.
  - Used to implement and validate the required business queries.


## What I Learned

This project helped me connect individual AWS services into an end-to-end data engineering story:

- Glue + Spark as the engine to turn raw S3 logs into a structured, partitioned dataset.
- Why Parquet and partitioning matter for Athena scan costs and performance.
- How Glue bookmarks (and similar features) make ETL jobs incremental—but still need validation and monitoring.
- Clear next steps like scheduling the ETL, adding monitoring/alerts, and introducing a dedicated “gold” layer for BI.

Overall, this capstone gave me a concrete system I can walk through in detail: how events are generated and stored, how they are processed, and how they are queried.
