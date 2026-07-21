
# Enterprise-AI-Knowledge-Platform

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Built with AWS](https://img.shields.io/badge/Built%20with-AWS-orange.svg)](https://aws.amazon.com/)
[![Amazon Kendra](https://img.shields.io/badge/Powered%20by-Amazon%20Kendra-ff9900.svg)](https://aws.amazon.com/kendra/)

## 🚀 Value Proposition

In the era of information overload, enterprises struggle to extract actionable insights from their vast repositories of unstructured data. The **Enterprise-AI-Knowledge-Platform** is designed to solve this challenge by providing a robust, automated pipeline for intelligent document processing and enterprise search. Leveraging the power of AWS, specifically Amazon Kendra and AWS Lambda, this platform automatically ingests, indexes, and makes searchable a wide variety of documents (PDFs, RTFs, etc.) the moment they are uploaded to Amazon S3. This solution empowers organizations to break down data silos, accelerate information discovery, and build a foundation for advanced Generative AI and Retrieval-Augmented Generation (RAG) applications.

## ✨ Features at a Glance

-   **Automated Data Ingestion**: Seamlessly triggers data synchronization jobs whenever new documents are uploaded to the designated Amazon S3 bucket, ensuring your knowledge base is always up-to-date.
-   **Intelligent Enterprise Search**: Powered by **Amazon Kendra**, the platform provides highly accurate, machine learning-powered search capabilities across all ingested documents.
-   **Serverless Architecture**: Utilizes **AWS Lambda** for event-driven processing, ensuring high availability, automatic scaling, and optimized cost-efficiency without the need to manage servers.
-   **Secure Access Management**: Implements granular IAM policies (provided in the `TrustRelationShips` directory) to ensure secure interactions between S3, Lambda, Kendra, and SageMaker.
-   **Multi-Format Support**: Capable of processing various document types, including PDFs and Rich Text Format (RTF) files, making it versatile for diverse enterprise data sources.
-   **Foundation for RAG**: Acts as the critical data ingestion and indexing layer required to build sophisticated Retrieval-Augmented Generation applications using Large Language Models (LLMs).

## 🏗️ Architecture Overview

The **Enterprise-AI-Knowledge-Platform** employs an event-driven, serverless architecture on AWS to automate the flow of information from storage to the search index.

### Key Components & Flow:

1.  **Document Storage (Amazon S3)**: Acts as the central repository for enterprise documents.
2.  **Event Trigger (S3 Event Notifications)**: When a new document (e.g., `AI_FM_Reports.pdf`) is uploaded to the S3 bucket, an event notification is automatically generated.
3.  **Event Processing (AWS Lambda)**: The `index.py` Lambda function is triggered by the S3 event. It extracts the bucket name and object key from the event payload.
4.  **Index Synchronization (Amazon Kendra)**: The Lambda function uses the `boto3` SDK to call the `start_data_source_sync_job` API on Amazon Kendra. This instructs Kendra to synchronize its index with the newly uploaded data in the S3 data source.
5.  **Intelligent Search**: Users or downstream applications (like SageMaker notebooks or Bedrock agents) can now query the updated Kendra index to retrieve highly relevant information from the newly added documents.

## 🚀 Getting Started

### Prerequisites

-   An active **AWS Account** with administrative permissions.
-   **AWS CLI** configured and authenticated.
-   An existing **Amazon S3 Bucket** for document storage.
-   An existing **Amazon Kendra Index** and **Data Source** configured to point to the S3 bucket.

### Deployment Steps

1.  **Clone the Repository**:
    ```bash
    git clone https://github.com/mahek-genai/Enterprise-AI-Knowledge-Platform.git
    cd Enterprise-AI-Knowledge-Platform
    ```

2.  **Configure IAM Roles**:
    Review and deploy the IAM policies provided in the `TrustRelationShips` directory and the root folder (e.g., `LambdaRolePolicy.rtf`, `KendraRolePolicyAccess.rtf`) to ensure the Lambda function has the necessary permissions to access S3 and trigger Kendra sync jobs.

3.  **Deploy the Lambda Function**:
    -   Create a new AWS Lambda function using Python 3.x.
    -   Copy the code from `index.py` into the Lambda function.
    -   Set the following Environment Variables in the Lambda configuration:
        -   `KENDRA_DATA_SOURCE_ID`: The ID of your Kendra Data Source.
        -   `KENDRA_INDEX_ID`: The ID of your Kendra Index.
    -   Attach the appropriate IAM role created in Step 2.

4.  **Configure S3 Event Trigger**:
    -   Navigate to your S3 bucket properties.
    -   Create an Event Notification for `All object create events` (`s3:ObjectCreated:*`).
    -   Set the destination to the Lambda function you just deployed.

5.  **Test the Pipeline**:
    Upload a sample document (like `TheLeaderboard_illusion.pdf`) to your S3 bucket. Check the Lambda logs in CloudWatch to verify the sync job was triggered, and then query your Kendra index to confirm the document is searchable.

## 🔒 Security & Compliance

-   **Least Privilege IAM**: The provided policy templates ensure that services only have the permissions strictly necessary to perform their functions.
-   **Data Encryption**: Ensure your S3 buckets and Kendra indexes are configured to use AWS KMS for data encryption at rest.

## 📄 License

This project is licensed under the Apache 2.0 License.

## 📞 Support

For any questions or issues, please open an issue on the [GitHub repository](https://github.com/mahek-genai/Enterprise-AI-Knowledge-Platform/issues).
