# AWS Stock Market Data Pipeline and Price Alert System

## 1. Project Overview

This project is a serverless AWS-based stock market data pipeline.

It retrieves stock market information for AAPL from the Alpha Vantage API, processes the data using AWS Lambda, stores the raw stock data in Amazon S3, catalogs the data using AWS Glue, and queries the stored data using Amazon Athena.

The project also uses Amazon DynamoDB to maintain alert information and Amazon SNS to send notifications when predefined stock price conditions are met.

The complete solution is built using managed AWS services without maintaining servers.

---

## 2. Project Architecture

The main data flow is:

Alpha Vantage API
        ↓
Amazon EventBridge
        ↓
AWS Lambda
        ↓
Amazon S3
        ↓
AWS Glue Crawler
        ↓
AWS Glue Data Catalog
        ↓
Amazon Athena
        ↓
Stock Data Analysis

The alert flow is:

AWS Lambda
        ↓
Price Threshold Check
        ↓
Amazon DynamoDB
        ↓
Amazon SNS
        ↓
Notification

---

## 3. Alpha Vantage API

Alpha Vantage is the external source of stock market data.

The API provides information such as:

- Stock symbol
- Opening price
- Highest price
- Lowest price
- Current price
- Trading volume
- Latest trading day
- Previous closing price
- Price change
- Percentage change

For this project, AAPL stock data is retrieved.

The API key is stored securely as an environment variable in Lambda instead of being hard-coded in the source code.

---

## 4. Amazon EventBridge

Amazon EventBridge is used to trigger the Lambda function automatically according to a schedule.

Instead of manually running the Lambda function every time, EventBridge can invoke it at a predefined interval.

Example:

EventBridge
    ↓
Triggers Lambda
    ↓
Lambda retrieves latest stock data

This makes the stock data collection process automatic.

---

## 5. AWS Lambda

AWS Lambda is the main processing component of the project.

The Lambda function:

1. Receives the EventBridge trigger.
2. Reads the Alpha Vantage API key from an environment variable.
3. Calls the Alpha Vantage API.
4. Retrieves the latest stock information.
5. Parses the API response.
6. Creates a structured JSON object.
7. Adds a timestamp to the data.
8. Stores the JSON file in Amazon S3.
9. Checks the stock price against the configured alert condition.
10. Stores alert information in DynamoDB when required.
11. Sends an SNS notification when the alert condition is triggered.

Lambda allows the entire process to run without managing servers.

---

## 6. Amazon S3

Amazon S3 is used as the main storage location for the raw stock market data.

The data is stored in the stock folder.

Example S3 path:

s3://stock-market-data-akku/stocks/

The files are stored with timestamp-based names.

Example:

AAPL_2026-08-07_08-10-27.json

Using timestamps prevents every new API response from overwriting the previous file.

Each JSON file contains information such as:

- symbol
- open
- high
- low
- price
- volume
- latest_trading_day
- previous_close
- change
- change_percent
- timestamp

---

## 7. AWS Glue

AWS Glue is used to discover and catalog the structure of the JSON data stored in S3.

A Glue Crawler scans the S3 location and identifies:

- Column names
- Data types
- Table structure
- Data location

The crawler creates a table in the AWS Glue Data Catalog.

The Glue Data Catalog acts as metadata storage that allows Athena to understand the structure of the files in S3.

---

## 8. Amazon Athena

Amazon Athena is used to query the stock data stored in S3.

Athena uses the Glue Data Catalog to understand the table structure.

Examples of analysis performed using Athena include:

- Viewing all stock records
- Counting records
- Finding the latest record
- Finding the highest stock price
- Finding the lowest stock price
- Calculating the average price
- Checking trading volume
- Checking duplicate records
- Identifying price increases and decreases

Athena allows SQL-based analysis without moving the data from S3 into a traditional database.

---

## 9. Amazon DynamoDB

Amazon DynamoDB is used to store stock price alert information.

When Lambda detects that a stock price meets the configured alert condition, an alert record can be stored in DynamoDB.

The alert information can include:

- Stock symbol
- Current price
- Threshold
- Alert type
- Timestamp
- Alert status

DynamoDB provides fast and scalable access to the alert records.

---

## 10. Amazon SNS

Amazon Simple Notification Service (SNS) is used for notifications.

When Lambda detects an alert condition:

Lambda
    ↓
SNS Topic
    ↓
Subscribed recipient
    ↓
Notification

For example, if the configured condition is that the AAPL price should trigger an alert when it crosses a particular threshold, Lambda can publish an alert message to SNS.

---

## 11. Complete Data Flow

The complete project works as follows:

### Step 1

EventBridge triggers the Lambda function according to the configured schedule.

### Step 2

Lambda reads the Alpha Vantage API key from its environment variable.

### Step 3

Lambda sends a request to Alpha Vantage.

### Step 4

Alpha Vantage returns the latest AAPL stock information.

### Step 5

Lambda processes and structures the response.

### Step 6

Lambda adds a timestamp to identify when the data was collected.

### Step 7

Lambda stores the JSON file in Amazon S3.

### Step 8

AWS Glue Crawler scans the S3 data and creates or updates the table metadata.

### Step 9

Athena uses the Glue Data Catalog to query the S3 data.

### Step 10

Lambda checks the current stock price against the configured alert condition.

### Step 11

If the alert condition is satisfied, the alert information is stored in DynamoDB.

### Step 12

Lambda publishes a notification through SNS.

---

## 12. Why This Architecture Is Serverless

The project does not require managing EC2 servers.

The main AWS services are managed services:

- Lambda for compute
- S3 for storage
- EventBridge for scheduling
- Glue for data cataloging
- Athena for querying
- DynamoDB for alert storage
- SNS for notifications

AWS manages the underlying infrastructure, allowing the project to scale without manually managing servers.

---

## 13. Key Benefits

### Automation

The stock data collection can run automatically using EventBridge.

### Scalability

AWS managed services can scale according to workload.

### Cost Efficiency

The project uses serverless services and only consumes resources when required.

### Data Analysis

Athena provides SQL-based analysis directly on S3 data.

### Historical Data

Timestamped S3 files allow multiple stock data snapshots to be retained.

### Alerting

Lambda, DynamoDB, and SNS provide an automated price alert mechanism.

### Security

Sensitive API credentials are stored using Lambda environment variables instead of being exposed in the source code.

---

## 14. Technologies Used

- Amazon Web Services (AWS)
- AWS Lambda
- Amazon S3
- Amazon EventBridge
- AWS Glue
- Amazon Athena
- Amazon DynamoDB
- Amazon SNS
- Alpha Vantage API
- Python
- SQL
- Git
- GitHub

---

## 15. Project Outcome

The final system provides an automated stock market data pipeline that collects stock information, stores historical snapshots, catalogs the data, enables SQL-based analysis, and supports automated price alerts and notifications.

The architecture demonstrates the use of AWS serverless, storage, analytics, database, scheduling, and notification services in a single end-to-end project.
