## Trigger AWS Lambda from a cross-account Amazon MSK

This repository contains AWS CloudFormation templates to setup producer and consumer accounts required for the blog - ![Trigger AWS Lambda from a cross-account Amazon MSK](https://blog-to-be-published). The blog provides the steps how you can trigger an AWS Lambda in the consumer account from a cross-account Amazon MSK in the producer account.

Here is the architecture that the cloudformation templates help you build -

![Architecture to demonstrate how to trigger AWS Lambda from cross-account Amazon MSK](https://github.com/aws-samples/lambda-cross-account-msk/blob/main/architecture.jpg?raw=true)

In the producer account, Amazon MSK cluster is deployed with the multi-VPC connectivity enabled. In the consumer account, Lambda ESM for Amazon MSK is deployed to invoke the Lambda function for the events in the cross-account Amazon MSK cluster.

The cloudformations can be deployed from the CLI or using the AWS Console.

## Setup

To setup the producer account run the Cloudformation template - 
```
aws cloudformation create-stack --stack-name producer-account-msk --template-body file://producer-account.yaml --capabilities CAPABILITY_NAMED_IAM
```

To setup the consumer account run the Cloudformation template - 
```
aws cloudformation create-stack --stack-name consumer-account-lambda --template-body file://consumer-account.yaml --parameters ParameterKey=MSKAccountId,ParameterValue=<<AWS Producer Account Id>> --capabilities CAPABILITY_NAMED_IAM
```

To setup the Amazon MSK cluster resource policy in producer account run the Cloudformation template - 
```
aws cloudformation create-stack --stack-name producer-msk-cluster-policy  --template-body file://producer-msk-cluster-policy.yaml --parameters ParameterKey=MSKClusterArn,ParameterValue=<<ARN of the Amazzon MSK cluster>> ParameterKey=LambdaRoleArn,ParameterValue=<<Arn of the Lambda role>> ParameterKey=LambdaAccountId,ParameterValue=<<AWS Consumer Account Id>> --capabilities CAPABILITY_NAMED_IAM
```

## Cleanup

To clean up the AWS resources, manually delete the Managed VPC Connection from the consumer account Amazon MSK console. Also delete the Lambda ESM for Amazon MSK from the AWS Lambda console.

Delete the Amazon MSK cluster resource policy from the producer account
```
aws cloudformation delete-stack --stack-name producer-msk-cluster-policy
```

Delete the resources created as a part of producer account setup
```
aws cloudformation delete-stack --stack-name producer-account-msk
```

Delete the resources created as a part of consumer account setup
```
aws cloudformation delete-stack --stack-name consumer-account-lambda
```

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

