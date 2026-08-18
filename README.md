# Enterprise AI Knowledge Platform

An AWS-based enterprise document ingestion and knowledge-search platform that automates the flow from documents in Amazon S3 to an Amazon Kendra index. The project is designed as a foundation for enterprise search and downstream Generative AI / RAG applications.

## What this project solves

Enterprise documents are often spread across reports, PDFs, and other unstructured sources. Manually keeping a search index synchronized is slow and unreliable.

This project uses an event-driven workflow so that when documents are uploaded to Amazon S3, AWS Lambda starts an Amazon Kendra data-source synchronization job. The indexed content can then be queried through Kendra and used as a retrieval layer for Generative AI applications.

## Architecture

```text
User / Document Source
        |
        v
   Amazon S3
        |
        | Object-created event
        v
   AWS Lambda
    (index.py)
        |
        | boto3: start_data_source_sync_job
        v
   Amazon Kendra
   S3 Data Source
        |
        v
 Enterprise Search / Retrieval
        |
        v
 GenAI / RAG Applications
```

## End-to-end workflow

1. Enterprise documents are uploaded to an Amazon S3 bucket.
2. S3 generates an object-created event.
3. The event invokes the Lambda function in `index.py`.
4. Lambda reads the S3 bucket and object key from the event.
5. Lambda creates an Amazon Kendra client using `boto3`.
6. Lambda starts a Kendra data-source synchronization job using `KENDRA_DATA_SOURCE_ID` and `KENDRA_INDEX_ID` environment variables.
7. Kendra synchronizes the S3 data source and makes the indexed content available for search.
8. The resulting knowledge layer can support enterprise search and downstream RAG / GenAI workflows.

The supplied implementation follows this exact S3 → Lambda → Kendra synchronization pattern. The Lambda reads the two Kendra identifiers from environment variables and calls `start_data_source_sync_job`. 

## Repository structure

```text
.
├── .gitattributes
├── AI_FM_Reports.pdf
├── AmazonSageMakerNotebooksServiceRolePolicy.rtf
├── KendraRolePolicyAccess.rtf
├── KendraRolePolicyS3Access.rtf
├── LambdaRolePolicy.rtf
├── ReaderAreTheLeaders.rtf
├── TheLeaderboard_illusion.pdf
├── TrustRelationShips/
│   └── TrustPolicy.json
├── index.py
└── README.md
```

## Key implementation

### `index.py`

The Lambda handler expects an S3 event and extracts:

- S3 bucket name
- URL-decoded object key

It then starts the Kendra data-source synchronization job using:

```python
kendra_client.start_data_source_sync_job(
    Id=KENDRA_DATA_SOURCE_ID,
    IndexId=KENDRA_INDEX_ID
)
```

The implementation uses these environment variables:

```text
KENDRA_INDEX_ID
KENDRA_DATA_SOURCE_ID
```

## AWS services used

- **Amazon S3** - document storage and event source
- **AWS Lambda** - event-driven synchronization trigger
- **Amazon Kendra** - enterprise search and indexing
- **Amazon SageMaker AI** - included in the project workflow for GenAI document querying / experimentation
- **AWS IAM** - service permissions and trust relationships
- **Amazon CloudWatch** - Lambda operational logging

## IAM and configuration files

The repository contains the supplied policy/reference files used during the AWS setup:

- `LambdaRolePolicy.rtf`
- `KendraRolePolicyAccess.rtf`
- `KendraRolePolicyS3Access.rtf`
- `AmazonSageMakerNotebooksServiceRolePolicy.rtf`
- `TrustRelationShips/TrustPolicy.json`

These files are retained as project reference material. Review and scope permissions according to the deployment environment rather than blindly applying broad permissions.

## Project reference documents

The repository also contains the documents used as knowledge-source examples and evaluation material:

- `AI_FM_Reports.pdf`
- `TheLeaderboard_illusion.pdf`
- `ReaderAreTheLeaders.rtf`

The included setup notes describe uploading selected documents to S3, creating the Lambda trigger, configuring Kendra Index/Data Source/DataSync, and using SageMaker AI Canvas for document querying. 

## Setup outline

### 1. Prepare AWS resources

Create or identify:

- An Amazon S3 bucket for source documents
- An Amazon Kendra index
- An S3-backed Kendra data source
- An AWS Lambda function
- Appropriate IAM roles and policies

### 2. Configure Lambda

Create a Python Lambda function and deploy `index.py`.

Set:

```text
KENDRA_INDEX_ID=<your-kendra-index-id>
KENDRA_DATA_SOURCE_ID=<your-kendra-data-source-id>
```

Attach an IAM execution role that permits the required Kendra operation and any required logging permissions.

### 3. Configure the S3 trigger

Create an S3 Object Created event notification and configure it to invoke the Lambda function.

### 4. Configure Kendra

Configure the Kendra index and S3 data source. The Lambda function starts synchronization when the S3 event occurs.

### 5. Test

Upload a supported document to the S3 data source and verify:

1. S3 receives the document.
2. Lambda is invoked.
3. CloudWatch contains the Lambda execution logs.
4. Kendra starts the data-source synchronization job.
5. The document becomes searchable through the Kendra index.

## Security notes

- Do not commit AWS access keys, secret keys, passwords, or environment-specific credentials.
- Replace placeholder resource identifiers with values from the target AWS account.
- Prefer least-privilege IAM policies for production deployments.
- Protect S3 data and Kendra indexes with appropriate encryption and access controls.
- The policy files in this repository are reference material and should be reviewed before deployment.

## Current project status

The AWS infrastructure used for the original project setup may need to be recreated in a target AWS account before the workflow can be executed again. The repository preserves the implementation, configuration references, setup material, and sample knowledge documents needed to understand and reproduce the architecture.

## Author

**Mahek Shaikh**

Generative AI Engineer | AWS | Amazon Bedrock | RAG

