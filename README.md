## Introduction

Many AWS users frequently rely on EC2 instances for their daily tasks. However, once these instances are started, they incur costs until explicitly stopped.

Using [EventBridge Scheduler](https://docs.aws.amazon.com/scheduler/latest/UserGuide/what-is-scheduler.html), you can automate the starting and stopping of EC2 instances **without writing any application code**.

Another advantage is that EventBridge Scheduler comes **at no additional cost for up to 14,000,000 invocations per month**.

This repository demonstrates how to use EventBridge Scheduler with an example.

## Starting an EC2 Instance

To start an EC2 instance for testing, replace `<SECURITY_GROUP_IDS>` and `<SUBNET_ID>` with actual values and execute the following command:

```shell
aws ec2 run-instances \
  --image-id ami-0f9fe1d9214628296 \
  --count 1 \
  --instance-type t2.micro \
  --security-group-ids <SECURITY_GROUP_IDS> \
  --subnet-id <SUBNET_ID>
```

## Creating AWS Resources

Refer to [template.yaml](template.yaml).

To deploy the CloudFormation stack, replace `<INSTANCE_ID>` with the actual instance ID(s) and run:

```shell
aws cloudformation deploy \
  --template-file ./template.yaml \
  --stack-name ec2-scheduler \
  --parameter-overrides InstanceIds='"<INSTANCE_ID1>", "<INSTANCE_ID2>"' \
  --capabilities CAPABILITY_NAMED_IAM
```

## Testing

The provisioned EventBridge Scheduler will automatically start EC2 instances at 7:00 AM and stop them at 10:00 PM.

If you need to modify the schedule, update the `ScheduleStartExpression` and `ScheduleStopExpression` parameters when deploying the CloudFormation stack.

Verify that the instances are started and stopped as expected by monitoring your EC2 dashboard or using AWS CLI commands.

## Cleaning Up

To avoid unnecessary charges, clean up the provisioned resources by running:

```shell
aws ec2 terminate-instances --instance-ids <INSTANCE_IDS>
aws cloudformation delete-stack --stack-name ec2-scheduler
```

## Conclusion

EventBridge Scheduler is an efficient tool for automating EC2 instance management, particularly for development and testing environments, where instances are not required 24/7.

Additionally, you can extend its use to other AWS services to automate recurring tasks such as backups, data processing, or scheduled maintenance.
