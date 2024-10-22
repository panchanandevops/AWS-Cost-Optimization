
# AWS Cloud Cost Optimization - Identifying Stale EBS Snapshots

## Overview
This AWS Lambda function is designed to manage Amazon Elastic Block Store (EBS) snapshots. It automatically deletes snapshots that are not attached to any active EC2 instance or whose associated volumes have been deleted. Additionally, snapshots older than 30 days that are not associated with active volumes are also deleted.

### Prerequisites
1. **AWS Account**: You need an AWS account to deploy and run the Lambda function.
2. **IAM Role with Permissions**: The Lambda function requires an IAM role with the following permissions:
   - `ec2:DescribeSnapshots`
   - `ec2:DescribeInstances`
   - `ec2:DescribeVolumes`
   - `ec2:DeleteSnapshot`
   
   Ensure the IAM role associated with the Lambda function has these permissions.

3. **AWS Lambda**: You need an active Lambda function setup. This Lambda function can be invoked on a schedule using AWS CloudWatch Events to run periodically (e.g., daily).

### Steps to Set Up

1. **Create the Lambda Function**:
   - Go to the [AWS Lambda Console](https://console.aws.amazon.com/lambda/).
   - Click **Create function**.
   - Choose **Author from scratch** and provide the necessary details such as function name and runtime (Python 3.x).

2. **Add Required IAM Permissions**:
   - Attach an IAM role with the following permissions to the Lambda function:
     ```json
     {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Effect": "Allow",
           "Action": [
             "ec2:DescribeSnapshots",
             "ec2:DescribeInstances",
             "ec2:DescribeVolumes",
             "ec2:DeleteSnapshot"
           ],
           "Resource": "*"
         }
       ]
     }
     ```

3. **Deploy the Code**:
   - Copy the Lambda function code (provided above) and paste it into the **Function code** section of the AWS Lambda console.
   - Click **Deploy** to save the changes.

4. **Set Up CloudWatch Event for Scheduling**:
   - Navigate to the **CloudWatch Console**.
   - In the **Events** section, create a new rule to trigger the Lambda function on a schedule (e.g., every day).
   - Define the rate or cron expression for the schedule (e.g., `rate(1 day)`).



## Modifying for 3-Minute Inactivity so that we can our lambda function quickly

To configure the Lambda function to delete snapshots that are not attached to any EC2 instance or whose associated volumes have been deleted after **3 minutes**, follow these steps:

1. **Update the Time Check in the Lambda Function Code**:
   - Modify the part of the code that checks how long a snapshot has been inactive. Instead of deleting snapshots older than 30 days, we will delete them after 3 minutes.

2. **Code Changes**:
   - Replace the line checking for a snapshot older than 30 days with a check for a snapshot that is older than 3 minutes. Update the relevant section of your code as follows:

   ```python
   # Replace this section:
   if datetime.utcnow() - snapshot_start_time > timedelta(days=30):

   # With this section:
   if datetime.utcnow() - snapshot_start_time > timedelta(minutes=3):
   ```

### Test the Lambda Function:
   - Test the function using sample data in the **Test** section of the Lambda console.
   - Ensure that the function executes without errors and handles snapshots and volumes correctly.


### Notes
- The Lambda function checks for snapshots that are not attached to any volume and deletes them. If the snapshot is associated with a volume that has been deleted, the snapshot is also deleted.
- Snapshots older than **3 minutes** from unattached volumes are automatically deleted.

## Acknowledgments

I would like to express my gratitude to my teacher, **Abhishek Veeramalla**, for his invaluable guidance and support throughout the development of this project. His expertise and dedication have been instrumental in shaping my understanding and skills. I highly recommend checking out his insightful tutorials on his YouTube channel [Abhishek Veeramalla](https://www.youtube.com/@AbhishekVeeramalla) for further learning and inspiration.
