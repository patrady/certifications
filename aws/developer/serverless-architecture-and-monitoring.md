# Serverless Architecture and Monitoring

## Building the Foundations of Serverless Architecture in AWS

![Serverless Architecture](./images/serverless-architecture-nutshell.png)

Serverless has the following benefits:

1. Don't manage the infastructure
2. Only pay for what you use
3. State is not saved between requests. State must be handled with a database like RDS
4. Lambdas are event based with triggers like CRON, HTTP, or messages

### Architecting a Serverless App

![Four Pillars of Serverless at Work](./images/four-pillars-of-serverless-at-work.png)

1. Computing: Do you need lambdas or servers?
2. Storage: How many S3 buckets do you need?
3. Persistance: Need RDS/DynamoDB or caching with Elasticache
4. Eventing: Other services like Api Gateway, Kinesis, or SQS

### The Serverless Framework

Let's you configure, test, develop, and deploy from a single configuration file. 

Benefits:
1. Simple and easy deployment
2. Easy to reproduce architecture
3. Local execution for development and debugging

## Going Serverless in AWS with Lambda

### Organizing Lambdas

Option 1: Lambda Monolith - All of your code is in one function
    - This is not ideal
Option 2: Create a lambda for each controller method

### Limits

Direct Upload Package Size: 50MB
Unzipped Package Size: 250MB
There's a limit on the number of concurrently running lambdas per account

### Metrics

AWS provides these Lambda metrics for free
1. Invocation Count
2. Execution Duration
3. Error Count
4. Throttle Count
5. Iterator Age
6. DLQ Errors

### Security

When a Lambda runs, it assums an IAM role
AWS recommends that each Lambda function has its own role that no other function shares! 

Approaches to storing resource credentials for your Lambda (from least -> most secure):
1. Store them in your code - DO NOT DO THIS
2. Store credentials as environment variables
    - These are encrypted at rest
    - This is in the Lambda >> Configuration >> Environment Variables
3. Store in the EC2 Parameter Store
    - Lets you access secrets over the command line
    - Adds some latency to the app but greatly improves security
4. Store in the Secrets Manager
    - Lets you access secrets through SDK
    - Has built-in support for RDS, DynamoDB, Redshift, external DBs
    - Can auto-rotate keys too!

### Stability

1. Monitor errors
2. Ensure that Lambda is processing all events that it receives

**Dead Letter Queue**: SNS Topic or SQS queue where triggering events are sent if the Lambda function errors out.
    - Support for this is built-in with the Lambda but you have to setup configurations to handle items in the DLQ

### Performance and Concurrency

There's a limit to how much memory a function can use. It defaults to 1024 MB
Increasing the memory will increase the amount of work the CPU can do, so if you have longer running functions - increase the memory.

**Provisioned Concurrency** - Reduces cold start latency by always having it running. This can get expensive

### Serverless Application Model (SAM)

![SAM vs Serverless Framework](./images/sam-vs-serverless-framework.png)

At the core of it, SAM is just a configuration file.

Resource Types

- `AWS::Serverless::Function`
    - Creates a lambda function, IAM execution role, and event source mappings
- `AWS::Serverless::API`
    - Creates an API Gateway mapping
    - Creates a Rest API, resources and methods, and Swagger configuration
- `AWS::Serverless::SimpleTable`
    - Creates a DynamoDB with no secondary indexes
    - Use normal DynamoDB type through cloud formation if more config is needed

How to deploy
`sam build && sam deploy`
