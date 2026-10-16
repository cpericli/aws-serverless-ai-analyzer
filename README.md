# aws-serverless-ai-analyzer
Serverless REST API for AI-powered text summarization and sentiment analysis using Python, AWS Lambda, API Gateway, and Amazon Bedrock.

# Serverless AI Text Analyzer

**Python | AWS Lambda | API Gateway | Amazon Bedrock | Amazon Nova Micro | Boto3**

A serverless REST API that uses generative AI to summarize text and classify sentiment. Built with Python and AWS, the application handles HTTP requests, validates user input, invokes an Amazon Bedrock foundation model, and returns structured JSON responses.

## Demo

**Demo screenshot:** <img width="860" height="262" alt="image" src="https://github.com/user-attachments/assets/0ca25d8f-0ad8-4998-ba8c-e8565bacf6b6" />

The API accepts text and returns a concise summary, sentiment classification, and estimated confidence score.

**Example request**

```json
{
  "text": "The software update improved performance, reduced crashes, and increased customer satisfaction."
}
```

**Example response**

```json
{
  "summary": "The software update improved performance and customer satisfaction.",
  "sentiment": "POSITIVE",
  "sentiment_score": 0.9
}
```

*Example output. Results may vary between model invocations.*

**Live execution:** Successfully deployed and tested through AWS API Gateway using PowerShell.

## System Architecture

```text
          HTTP POST /analyze
                  |
                  v
        Amazon API Gateway
                  |
                  v
              AWS Lambda
              Python 3.13
                  |
         +--------+---------+
         |                  |
   Input Validation    Error Handling
         |                  |
         v                  |
    Amazon Bedrock          |
     Converse API           |
         |                  |
         v                  |
    Amazon Nova Micro       |
         |                  |
         v                  |
   Response Validation      |
         |                  |
         +--------+---------+
                  |
                  v
          Structured JSON
                  |
                  v
              HTTP Client

       Amazon CloudWatch Logs
```

### Request Processing

1. API Gateway receives an HTTP POST request at `/analyze`.
2. AWS Lambda parses the request and validates the submitted text.
3. Lambda invokes Amazon Nova Micro through the Amazon Bedrock Converse API.
4. The model generates a summary, sentiment classification, and estimated confidence score.
5. Lambda parses and validates the AI-generated response.
6. API Gateway returns structured JSON to the client.

## Technical Implementation

### API Design

- Serverless HTTP API using AWS Lambda and API Gateway
- JSON request and response format
- Input length limit of 10,000 characters
- Consistent HTTP status codes and error messages
- Stateless request processing

### Reliability and Error Handling

The application validates requests before invoking the AI model, preventing unnecessary model calls for invalid input.

It handles malformed JSON, missing or empty text, incorrect input types, and oversized requests.

Model responses are also validated to ensure that:

- The summary is a non-empty string.
- Sentiment is POSITIVE, NEGATIVE, or NEUTRAL.
- The confidence score is numeric and between 0 and 1.
- Invalid model output produces a controlled error response.

### Maintainability

The Python implementation separates responsibilities into reusable functions:

- `validate_input()` — Request parsing and validation
- `invoke_model()` — Amazon Bedrock integration
- `parse_model_response()` — AI response parsing and validation
- `create_response()` — HTTP response formatting
- `lambda_handler()` — Request orchestration and exception handling

### Security Considerations

- AWS IAM controls the Lambda function's access to Amazon Bedrock.
- Input text is explicitly treated as data rather than executable instructions.
- API responses avoid exposing internal exception details.
- Application errors are recorded through Python logging and Amazon CloudWatch.

## API Reference

### POST /analyze

Accepts a JSON object containing a `text` field.

**Request**

```json
{
  "text": "Text to summarize and analyze."
}
```

**Successful response: HTTP 200**

```json
{
  "summary": "Generated summary",
  "sentiment": "NEUTRAL",
  "sentiment_score": 0.85
}
```

**Error responses**

| Status | Description |
|---|---|
| 200 | Analysis completed successfully |
| 400 | Invalid request or input |
| 502 | Model invocation failed or returned invalid data |
| 500 | Unexpected internal server error |

## Technology Stack

| Technology | Purpose |
|---|---|
| Python 3.13 | Backend application logic |
| AWS Lambda | Serverless compute |
| Amazon API Gateway | HTTP API routing |
| Amazon Bedrock | Managed foundation model access |
| Amazon Nova Micro | Text summarization and sentiment analysis |
| Boto3 | AWS SDK integration |
| AWS IAM | Service permissions |
| Amazon CloudWatch | Application logging |

## Configuration and Deployment

The application is deployed in AWS US East (N. Virginia).

The Lambda function uses the following environment variable:

```text
BEDROCK_MODEL_ID=amazon.nova-micro-v1:0
```

To deploy the application in another AWS account:

1. Create a Python 3.13 AWS Lambda function.
2. Upload `lambda_function.py`.
3. Configure the Lambda execution role with Bedrock invocation and CloudWatch logging permissions.
4. Set the `BEDROCK_MODEL_ID` environment variable.
5. Create an API Gateway HTTP API with a `POST /analyze` route integrated with Lambda.
6. Deploy the API and test it using an HTTP client.

Amazon Bedrock model availability and access permissions depend on the AWS account and Region.

## Future Improvements

- Automated unit testing with pytest
- GitHub Actions continuous integration
- Infrastructure as code using AWS SAM
- API authentication and rate limiting for public demo access

## Project Status

The backend API is deployed and operational. Text summarization and sentiment analysis have been successfully tested through API Gateway.

The repository contains the Python application source. Automated testing and infrastructure-as-code configuration are planned improvements.
