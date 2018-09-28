# Cloud Formation templates to create VPC prerequisites, RDS instance, and Lambda 

Create Cloud Formation stacks:
 - vpc.yml - Network prerequisites to support RDS and Lambda
 - rds.yml - RDS instance
 - lambda.yml - Lambda function

RDS is accessible on private Subnets only.

Lambda has a public route to the internet to enable external API calls.

## Setup

### VPC

```
aws cloudformation package --template-file ./cf/vpc.yml --s3-bucket deploy --output-template-file ./cf/out/vpc.out.yml
aws cloudformation validate-template --template-body file://cf/out/vpc.out.yml
aws cloudformation deploy --template-file ./cf/out/vpc.out.yml --stack-name dev-vpc --parameter-overrides $(cat ./cf/params/dev.params) --capabilities CAPABILITY_NAMED_IAM
```

### RDS

```
aws cloudformation package --template-file ./cf/rds.yml --s3-bucket deploy --output-template-file ./cf/out/rds.out.yml
aws cloudformation validate-template --template-body file://cf/out/rds.out.yml
aws cloudformation deploy --template-file ./cf/out/rds.out.yml --stack-name dev-rds --parameter-overrides $(cat ./cf/params/dev.params) --capabilities CAPABILITY_NAMED_IAM
```

### Lambda

```
aws cloudformation package --template-file ./cf/lambda.yml --s3-bucket deploy --output-template-file ./cf/out/lambda.out.yml
aws cloudformation validate-template --template-body file://cf/out/lambda.out.yml
aws cloudformation deploy --template-file ./cf/out/lambda.out.yml --stack-name dev-lambda --parameter-overrides $(cat ./cf/params/dev.params) --capabilities CAPABILITY_NAMED_IAM
```

## Deploy new Lambda function code



```
zip -r lambda.zip * -x *.zip
aws s3 cp lambda.zip s3://deploy/lambda.zip
aws lambda update-function-code --function-name dev-lambda-LambdaFunction-Y1MY9KIDUO0E --s3-bucket deploy --s3-key lambda.zip
```