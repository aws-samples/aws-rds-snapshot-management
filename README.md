## AWS RDS Snapshot Management

The RDS Snapshot Management tool automates the management of manual snapshotting for both Amazon RDS Instances and Aurora Clusters. This solution creates manual snapshots, deletes  old snapshots, and finally sends a summary email. The snapshot management tool allows you to specify the backup schedule (at what times and how often), retention period and works only on the exact the list of RDS Instances and Aurora Clusters specified. 

**Note, this solution has few limitations -** 
a)	Solution is dependent on AWS Step Functions and hence this solution is available only in Regions where AWS Step Functions are supported.
b)	Solution is not built to work with encrypted manual snapshots


## The procedure for deploying this architecture on AWS consists of the following steps. 
1. Download CloudFormation script (YAML file) and Lambda functions (listed below)
   - *check_snapshots.zip*
   - *delete_old_snapshots_rds.zip*
   - *email_notification.zip*
   - *take_snapshots_rds.zip*

2. Upload Lambda functions into an S3 Bucket (CodeBucket in Step 4)
   - Upload Lambda functions (zip files) into an S3 bucket of your choice.
   - Lambda functions in an S3 bucket should be in the same Region where the CloudFormation script is executed.

3. Execute the CloudFormation script
   - Upload AWS CloudFormation template in the Region of your choice.
   - Provide a unique stack name.
   - Lambda functions in an S3 bucket should be in the same Region where the CloudFormation script is executed.

4. Launch the stack
   - Launch the AWS CloudFormation template in your AWS account.
   - Enter Input parameter values for the stack.
     - *Backup Interval - Interval for backups in hours. Default is set to 24 hrs.*
     - *Backup Schedule – To be provided in CloudWatch Event Cron format. Run at least once for every interval. The default   value runs once every at 1:00 AM UTC. For more information, refer to CloudWatch schedule events expression - AWS documentation.*
     - *CodeBucket - Name of the bucket that contains the Lambda functions that were uploaded in Step 2, to deploy.*
     - *DeleteOldSnapshots – Can choose either TRUE or FALSE. Set to TRUE to enable deletion of snapshot based on RetentionDays. Set to FALSE to disable deletion, thus historical information is available in DynamoDB.*
     -	*LogLevel - Log level for Lambda functions. Valid values are one of DEBUG, INFO, WARN, ERROR, CRITICAL.*
     -	*NotifyEmail – Email address required to send notifications. Confirm the subscription sent by SNS is be able to receive email notifications.*
     -	*Retention Days – Value is in days, to keep snapshots before deleting them. Default is seven days.*

5. Options and review for CloudFormation
   - Choose Next for Options screen on CloudFormation.
   - On the Review page, let AWS handle creation of an IAM role based on the components created.
   - Check the box for I acknowledge that AWS CloudFormation might create IAM resources with custom names.
   - Choose Next to execute the CloudFormation script.

6. Run the CloudFormation script
   - Wait until the stack creation has status CREATE_COMPLETE.
   - Check the Outputs tab of CloudFormation stack for: 
     - *S3SourceListBucketOutput - A new S3 bucket is created where the rds_backup_list.txt file can be uploaded.*
     - *BackupFailedTopic – An SNS topic to receive alerts of failed backups.*
     - *EmailNotificationTopic – An SNS topic to receive notifications of newly created snapshots and deleted snapshots.*

7. Upload rds_backup_list.txt
   - The rds_backup_list.txt file should contain the list of RDS DB instance or Aurora cluster names to be backed-up. 
   - Each DB instance name appears on a separate line of the text file.
   - Upload this file in the newly created S3 bucket available as Output from CloudFormation script as S3SourceListBucketOutput. 

8. Test the tool
   - Go to the Step Function console and choose the state machine created by the CloudFormation script. 
   - Choose New Execution to test the tool.
   - Provide an Execution name. For example, BlogTest1, and choose Start Execution to perform a test.
   - See the visual workflow where various functions of the state machine are triggered. 
   - You can see the first set of snapshots are created for the databases listed in rds_backup_list.txt file.
   - Optionally, check the CloudWatch Rules to see the Backup Schedule created as defined in Step 4. You can also review the email notification that SNS sends when the steps are completed. An example email is shown here.

#### Author - Suman Koduri

**Note: Refer to this [RDS tool](https://github.com/awslabs/rds-snapshot-tool)** if one is looking to automate the task of creating manual snapshots, copying them into a different account and a different region, and deleting them after a specified number of days

## License

This library is licensed under the Apache 2.0 License. 
