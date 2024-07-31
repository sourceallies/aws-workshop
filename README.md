# AWS Workshop
Welcome to the AWS Workshop! By the end of this workshop, you will have a basic understanding of key AWS services and how to deploy a few resources using infrastructure as code.

## Workshop Goals
- Understand the basics of AWS and its key services.
- Learn how to set up your development environment.
- Deploy AWS Lambda and S3 resources using the cli and a cloudformation template.

## Machine Setup Checklist
Before we begin, please ensure you have the following installed and set up on your machine:

- [ ] **Python**: Download and install Python from [python.org](https://www.python.org/downloads/). Verify the installation by running `python --version`. Ensure it is a 3.x version.
- [ ] **AWS CLI**: Install the AWS CLI from [aws.amazon.com/cli](https://aws.amazon.com/cli/), or use pip `pip install awscli`. Verify the installation by running `aws --version`.
- [ ] **SAM CLI**: Follow the installation instructions for the AWS SAM CLI from [AWS documentation](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/install-sam-cli.html). Verify the installation by running `sam --version`.
- [ ] **Code Editor**: Install a code editor of your choice (e.g., VS Code, Sublime Text).
- [ ] **AWS Authentication**:
  - Go to the Source Allies AWS SSO page that lists our AWS accounts at [Source Allies AWS SSO](https://allies.awsapps.com/start/#/?tab=accounts).
  - Use the Source Allies Dev account. Expand the account and click the "Access Keys" link. Use Option 1 to obtain your access key, secret, and session token, and input them into your terminal (shell).
  - Verify by running `aws s3 ls`, which will list all S3 buckets in the account.

## Key AWS Services
AWS offers a vast array of services. In this workshop, we will focus on a few essential ones:

- **S3**: Simple Storage Service for storing and retrieving any amount of data.
- **CloudFormation**: Infrastructure as Code (IaC) service for managing AWS resources.
- **Lambda**: Serverless compute service that runs code in response to events.
- **IAM**: Identity and Access Management for controlling access to AWS resources.

## Working with S3 Buckets Using AWS CLI

### Creating an S3 Bucket

1. Run the following command, replacing `your-bucket-name` with a unique name for your bucket:
    ```bash
    aws s3 mb s3://your-bucket-name
    ```
1. Verify that the bucket was created by viewing the bucket in the AWS web console or listing your S3 buckets aws cli:
    ```bash
    aws s3 ls
    ```

For more details, refer to the [AWS CLI S3 mb documentation](https://docs.aws.amazon.com/cli/latest/reference/s3/mb.html).

### Creating a Local File and Upload it to your S3 Bucket

1. Run the following command to create a simple text file named `example.txt` with some content:
    ```bash
    echo "Hello, AWS S3!" > example.txt
    ```
1. Run the following command, replacing `your-bucket-name` with the name of your S3 bucket:
    ```bash
    aws s3 cp example.txt s3://your-bucket-name/
    ```
1. Verify that the file was uploaded by listing the contents of the bucket:
    ```bash
    aws s3 ls s3://your-bucket-name/
    ```

For more details, refer to the [AWS CLI S3 cp documentation](https://docs.aws.amazon.com/cli/latest/reference/s3/cp.html).

### Deleting an S3 Bucket

1. Ensure the bucket is empty. If it contains objects, delete them first:
    ```bash
    aws s3 rm s3://your-bucket-name --recursive
    ```
1. Run the following command to delete the bucket, replacing `your-bucket-name` with the name of your bucket:
    ```bash
    aws s3 rb s3://your-bucket-name
    ```
1. Verify that the bucket was deleted by viewing in the AWS web console or listing your S3 buckets with aws cli:
    ```bash
    aws s3 ls
    ```

For more details, refer to the [AWS CLI S3 rb documentation](https://docs.aws.amazon.com/cli/latest/reference/s3/rb.html).

### Additional Resources
- [AWS CLI Command Reference](https://docs.aws.amazon.com/cli/latest/reference/)
- [AWS CLI S3 Commands](https://docs.aws.amazon.com/cli/latest/reference/s3/index.html)

## Working with CloudFormation to Create an S3 Bucket

### Creating a CloudFormation Template

1. Create a CloudFormation template file named `template.yaml` with the following content:
    ```yaml
    AWSTemplateFormatVersion: '2010-09-09'
    
    Resources:
      Bucket:
        Type: 'AWS::S3::Bucket'
    ```

### Deploying the CloudFormation Template

1. Run the following command to deploy the CloudFormation stack, replacing `your-stack-name` with a unique name for your stack and ensuring the `--template-file` path points to your `template.yaml` file:
    ```bash
    aws cloudformation deploy --stack-name your-stack-name --template-file template.yaml --capabilities CAPABILITY_NAMED_IAM
    ```
1. Wait for the stack creation to complete. You can monitor the progress in the AWS web console under the cloudformation service. Or, using the following command:
    ```bash
    aws cloudformation describe-stacks --stack-name your-stack-name
    ```
    The stack status should eventually change to `CREATE_COMPLETE`.

For more details, refer to the [AWS CLI CloudFormation deploy documentation](https://docs.aws.amazon.com/cli/latest/reference/cloudformation/deploy/index.html).

### Deleting the CloudFormation Stack
To delete the CloudFormation stack and the associated S3 bucket, follow these steps:

1. Run the following command, replacing `your-stack-name` with the name of your stack:
    ```bash
    aws cloudformation delete-stack --stack-name your-stack-name
    ```
2. Verify that the stack was deleted in the AWS web console.

For more details, refer to the [AWS CLI CloudFormation delete-stack documentation](https://docs.aws.amazon.com/cli/latest/reference/cloudformation/delete-stack.html).

### Additional Resources
- [AWS CloudFormation Documentation](https://docs.aws.amazon.com/cloudformation/index.html)
- [AWS Cloudformation S3 Bucket Resource Documentation](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-resource-function.html)
- [AWS CLI CloudFormation Commands](https://docs.aws.amazon.com/cli/latest/reference/cloudformation/index.html)

## Use SAM CLI Instead of Cloudformation Service with AWS CLI

SAM CLI provides several features making deployments from cli much easier than using aws cli directly.

Run the following to deploy your cloudformation template using sam cli:
```bash
    sam deploy --guided
```
You will be prompted to enter information in a step-by-step way:
- Provide a stack name of your choice:
- All other options can be the defaults. Just hit enter to use the default.

SAM CLI will deploy your template with the stack name you provided, and it will create a config file name samconfig.toml which saves your config provided during the `--guided` process. For subsequent deployments (after making additional changes to the cloudformation template), you should run `sam deploy` without the `--guided` option: SAM CLI will using the config in the samconfig.toml file.

### Additional Resources
- [SAM CLI Documentation](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-command-reference.html)

## Deploy a Lambda Function Using the SAM CLI

Deploying lambdas can be tricky because you need to package your lambda code, upload it to an S3 bucket, deploy your lambda with a reference to the lambda package, and update the lambda version. While that exercise is educational, we will skip to the easier version, using SAM's special cloudformation type `aws::serverless::function`.

Update your cloudformation template (template.yaml file) with the following. Notice the serverless transform at the top of the template. Notice the type of the function resource; it's "service" is `serverless`.

```yaml
    AWSTemplateFormatVersion: '2010-09-09'
    Transform: 'AWS::Serverless-2016-10-31'

    Resources:
        Bucket:
            Type: 'AWS::S3::Bucket'

        HelloWorldFunction:
            Type: 'AWS::Serverless::Function'
            Properties:
                Handler: 'app.handler'
                Runtime: 'python3.12'
                CodeUri: './'
```

Create a python file name `app.py` in the same directory as the cloudformation template with the following handler method:

```python
    def handler(event, context):
        print("Hello, World.")
        return "Hello, World."
```

Deploy your lambda function by running `sam deploy`. SAM CLI will do the rest!

## Modify the Lambda Function to Read from S3

If we have time, we'll update our lambda function code to get an object (file) from S3 and read the content of the file. While this sounds simple enough, it opens up two important concepts:
1. building lambda code before deployment
1. IAM permissions

Introducing these concepts can be challenging if you haven't encountered them before.