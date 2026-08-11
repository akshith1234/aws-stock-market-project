# AWS Stock Market Data Pipeline & Price Alert System

## 📌 Project Overview

This project is an end-to-end serverless stock market data pipeline built using AWS.

The system automatically retrieves stock market data from the Alpha Vantage API, processes the data using AWS Lambda, stores historical stock records in Amazon S3, catalogs the data using AWS Glue, and enables SQL-based analysis through Amazon Athena.

The project also implements a stock price alert mechanism using Amazon DynamoDB and Amazon SNS.

---

## 🏗️ Architecture

```text
                    Alpha Vantage API
                           │
                           ▼
                    Amazon EventBridge
                           │
                           ▼
                       AWS Lambda
                    ┌──────┴──────┐
                    │                 │
                    ▼                 ▼
               Amazon S3          Price Check
                    │                 │
                    ▼                 ▼
                AWS Glue           DynamoDB
                    │                 │
                    ▼                 ▼
                Amazon            Amazon SNS
                 Athena                │
                    │                  ▼
                    ▼             Notification
              SQL Analysis