# Sample script to create the infrastructure and the serive

We can spin up the stacks using the AWS CLI with this Bash script:

`
aws cloudformation create-stack \
  --stack-name reflectoring-network \
  --template-body file://network.yml \
  --capabilities CAPABILITY_IAM

aws cloudformation wait stack-create-complete --stack-name reflectoring-network

aws cloudformation create-stack \
  --stack-name reflectoring-service \
  --template-body file://service.yml \
  --parameters \
      ParameterKey=StackName,ParameterValue=reflectoring-network \
      ParameterKey=ServiceName,ParameterValue=reflectoring-hello-world \
      ParameterKey=ImageUrl,ParameterValue=docker.io/reflectoring/aws-hello-world:latest \
      ParameterKey=ContainerPort,ParameterValue=8080 \
      ParameterKey=HealthCheckPath,ParameterValue=/hello \
      ParameterKey=HealthCheckIntervalSeconds,ParameterValue=90

aws cloudformation wait stack-create-complete --stack-name reflectoring-service

`

### Updating a CloudFormation Stack

1. AWS cloudformation ChangeSet: To create a changeset, we use the create-change-set command
`
aws cloudformation create-change-set \
  --change-set-name update-reflectoring-service \
  --stack-name reflectoring-service \
  --use-previous-template \
  --parameters \
      ParameterKey=ImageUrl,ParameterValue=docker.io/reflectoring/aws-hello-world:v4 \
      ... more parameters
`
After having created a changeset, we can review it in the AWS console or with this CLI command:

`
aws cloudformation describe-change-set \
  --stack-name reflectoring-service \
  --change-set-name update-reflectoring-service
`

When we’re happy with the changes, we can execute the changeset:

`
aws cloudformation execute-change-set \
  --stack-name reflectoring-service \
  --change-set-name update-reflectoring-service
`

Other options of updating cloudformation stack 

2. Updating the Service Stack

`
aws cloudformation update-stack \
  --stack-name reflectoring-service \
  --use-previous-template \
  --parameters \
      ParameterKey=ImageUrl,ParameterValue=docker.io/reflectoring/aws-hello-world:v3 \
      ... more parameters

aws cloudformation wait stack-update-complete --stack-name reflectoring-service
`

3. Delete and Re-create a Granular Stack
4. Update the ECS Service via the API


> Thanks to [Tom Hombergs](https://reflectoring.io/aws-cloudformation-ecs-deployment/)  