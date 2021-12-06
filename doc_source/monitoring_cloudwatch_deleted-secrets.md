# Monitor secrets scheduled for deletion<a name="monitoring_cloudwatch_deleted-secrets"></a>

You can use a combination of AWS CloudTrail, Amazon CloudWatch Logs, and Amazon Simple Notification Service \(Amazon SNS\) to create an alarm that notifies you of any attempts to access a secret pending deletion\. If you receive a notification from an alarm, you might want to cancel deletion of the secret to give yourself more time to determine if you really want to delete it\. Your investigation might result in the secret being restored because you still need the secret\. Alternatively, you might need to update the user with details of the new secret to use\.

The following procedures explain how to receive a notification when a request for the `GetSecretValue` operation that results in a specific error message written to your CloudTrail log files\. Other API operations can be performed on the secret without triggering the alarm\. This CloudWatch alarm detects usage that might indicate a person or application using outdated credentials\.

Before you begin these procedures, you must turn on CloudTrail in the AWS Region and account where you intend to monitor AWS Secrets ManagerAPI requests\. For instructions, go to [Creating a trail for the first time](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-a-trail-using-the-console-first-time.html) in the *AWS CloudTrail User Guide*\.

## Step 1: Configure CloudTrail log file delivery to CloudWatch logs<a name="monitoring_cloudwatch_deleted-secrets_part1"></a>

You must configure delivery of your CloudTrail log files to CloudWatch Logs\. You do this so CloudWatch Logs can monitor them for Secrets Manager API requests to retrieve a secret pending deletion\.

**To configure CloudTrail log file delivery to CloudWatch Logs**

1. Open the CloudTrail console at [https://console\.aws\.amazon\.com/cloudtrail/](https://console.aws.amazon.com/cloudtrail/)\.

1. On the top navigation bar, choose the AWS Region to monitor secrets\.

1. In the left navigation pane, choose **Trails**, and then choose the name of the trail to configure for CloudWatch\.

1. On the **Trails Configuration** page, scroll down to the **CloudWatch Logs** section, and then choose the edit icon \(![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/edit-pencil-icon.png)\)\.

1. For **New or existing log group**, type a name for the log group, such as **CloudTrail/MyCloudWatchLogGroup**\.

1. For **IAM role**, you can use the default role named **CloudTrail\_CloudWatchLogs\_Role**\. This role has a default role policy with the required permissions to deliver CloudTrail events to the log group\.

1. Choose **Continue** to save your configuration\.

1. On the **AWS CloudTrail will deliver CloudTrail events associated with API activity in your account to your CloudWatch Logs log group** page, choose **Allow**\.

## Step 2: Create the CloudWatch alarm<a name="monitoring_cloudwatch_deleted-secrets_part2"></a>

To receive a notification when a Secrets Manager `GetSecretValue` API operation requests to access a secret pending deletion, you must create a CloudWatch alarm and configure notification\.

**To create a CloudWatch alarm**

1. Sign in to the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. On the top navigation bar, choose the AWS Region where you want to monitor secrets\.

1. In the left navigation pane, choose **Logs**\.

1. In the list of **Log Groups**, select the check box next to the log group you created in the previous procedure, such as **CloudTrail/MyCloudWatchLogGroup**\. Then choose **Create Metric Filter**\.

1. For **Filter Pattern**, type or paste the following:

   ```
   { $.eventName = "GetSecretValue" && $.errorMessage = "*secret because it was marked for deletion*" }
   ```

   Choose **Assign Metric**\.

1. On the **Create Metric Filter and Assign a Metric** page, do the following:

   1. For **Metric Namespace**, type **CloudTrailLogMetrics**\.

   1. For **Metric Name**, type **AttemptsToAccessDeletedSecrets**\.

   1. Choose **Show advanced metric settings**, and then if necessary for **Metric Value**, type **1**\.

   1. Choose **Create Filter**\.

1. In the filter box, choose **Create Alarm**\.

1. In the **Create Alarm** window, do the following:

   1. For **Name**, type **AttemptsToAccessDeletedSecretsAlarm**\.

   1.  **Whenever:**, for **is:**, choose **>=**, and then type **1**\.

   1. Next to **Send notification to:**, do one of the following:
      + To create and use a new Amazon SNS topic, choose **New list**, and then type a new topic name\. For **Email list:**, type at least one email address\. You can type more than one email address by separating them with commas\.
      + To use an existing Amazon SNS topic, choose the name of the topic to use\. If a list doesn't exist, choose **Select list**\.

   1. Choose **Create Alarm**\.

## Step 3: Test the CloudWatch alarm<a name="monitoring_cloudwatch_deleted-secrets_part3"></a>

To test your alarm, create a secret and then schedule it for deletion\. Then, try to retrieve the secret value\. You shortly receive an email at the address you configured in the alarm\. It alerts you to the use of a secret scheduled for deletion\.