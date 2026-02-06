Event-Driven Serverless Order Processing System

A high-scale, resilient, and decoupled order fulfillment system built using a "Cloud-First" approach on AWS. This project demonstrates modern architectural patterns like asynchronous processing, load leveling, and idempotent consumers.

🚀 Overview

This repository contains the architecture and implementation details for a serverless e-commerce backend. Instead of a traditional synchronous flow where the user waits for a database write, this system uses a "Pull-based" model. It immediately returns an HTTP 202 Accepted response to the client and processes the order in the background, ensuring the system remains responsive even under heavy load.

🏗️ Architecture

The system utilizes a variety of AWS services to ensure high availability and scalability:

API Gateway (HTTP API): Low-latency entry point for client requests.

AWS Lambda (SubmitOrder): Validates the incoming request and pushes the payload to the queue.

Amazon SQS (Standard Queue): Acts as a buffer/decoupler between submission and processing.

AWS Lambda (ProcessOrder): The worker function that handles fulfillment logic and database persistence.

Amazon DynamoDB: A NoSQL database storing order records with on-demand scaling.

Amazon SES: Handles transactional email notifications.

Amazon CloudWatch: Provides centralized logging and proactive error alerting.

System Flow

graph TD
    A[Client] -->|POST /orders| B(API Gateway)
    B -->|Invoke| C[Lambda: SubmitOrder]
    C -->|202 Accepted| A
    C -->|Push Message| D[SQS: OrderQueue]
    D -->|Trigger Batch| E[Lambda: ProcessOrder]
    E -->|PutItem| F[(DynamoDB: Orders)]
    E -->|Send Email| G[Amazon SES]
    D -.->|Failure| I[SQS: Dead Letter Queue]


🛠️ Key Engineering Patterns

Load Leveling: Amazon SQS buffers incoming messages, protecting downstream resources (like DynamoDB or third-party APIs) from traffic spikes.

Idempotency: The processing logic uses the unique orderId to ensure that duplicate messages do not result in duplicate database entries.

Poison Message Handling: Implemented a Dead Letter Queue (DLQ) with a redrive policy to capture and isolate messages that fail processing after 3 attempts.

Least-Privilege Security: Each Lambda function is assigned a specific IAM role granting only the permissions necessary to perform its task.

📈 Performance Indicators

Latency: < 1s submission time due to asynchronous decoupling.

Scalability: Fully managed serverless services that scale automatically with demand.

Cost-Efficiency: Utilizes AWS Free Tier eligible services; zero cost for idle time.

🛣️ Roadmap

[ ] Phase 2: Integrate AWS Step Functions to manage complex shipping and refund workflows.

[ ] Phase 3: Implement Amazon EventBridge to fan-out events to other microservices (Inventory, Analytics).

[ ] Phase 4: Add Amazon Cognito for secure user authentication.

👤 Author

Dhruv Prajapati

LinkedIn

GitHub
