# Real-Time Processing with AWS SQS and SNS - PoC

This Proof of Concept (PoC) demonstrates how to use AWS SQS and SNS for real-time processing in a microservices architecture. It simulates a scenario where product updates are sent from a vendor system, queued in SQS, and processed by a Lambda function.

## Folder Structure

- **src/**: Contains the Lambda function code and dependencies.
- **infrastructure/**: Contains the CloudFormation templates to deploy the required AWS resources.
- **README.md**: Instructions on how to deploy and use the PoC.

## Prerequisites

1. **AWS CLI**: Ensure the AWS CLI is installed and configured with your credentials.
2. **AWS Account**: You need an AWS account with permissions to create the necessary resources (S3, Lambda, SQS, SNS).

## Steps to Deploy

1. **Upload Lambda Code to S3**:
   - Zip the contents of the `src/` folder.
   - Upload the zip file to an S3 bucket.

   ```zip -r lambda.zip src/```
   ```aws s3 cp lambda.zip s3://your-s3-bucket/lambda.zip```

2. **Deploy CloudFormation Templates**:
    - Deploy SQS and SNS
    ```aws cloudformation create-stack --stack-name sqs-sns-poc --template-body file://infrastructure/sqs_queue.yml --capabilities CAPABILITY_IAM```
    ```aws cloudformation create-stack --stack-name sns-topic-poc --template-body file://infrastructure/sns_topic.yml --capabilities CAPABILITY_IAM```

    - Deploy Lambda
    ```aws cloudformation create-stack --stack-name lambda-poc --template-body file://infrastructure/lambda.yml --capabilities CAPABILITY_IAM```


3. **Test the Setup**:
- Publish the message
    ```aws sns publish --topic-arn arn:aws:sns:your-region:your-account-id:product-updates-topic --message '{"product_data": {"id": "123", "name": "New Product", "price": 29.99}}'```

## How It Works

1. Vendor triggers a product update: This could be an update in a product's price, inventory, or description.
2. Message sent to SNS topic: The vendor's update triggers an event that sends a message to the product-updates-topic.
3. SQS queue receives the message: The SNS topic forwards the message to the product-updates-queue.
4. Lambda function processes the message: The Lambda function is triggered by the SQS queue, processes the product update, and simulates updating the Magento system.