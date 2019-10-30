# Monitor the Use of Your AWS Secrets Manager Secrets<a name="monitoring"></a>

As a best practice, you should monitor your secrets to make sure that usage of your secrets and any changes to them are logged\. This helps you to ensure that any unexpected usage or change can be investigated, and unwanted changes can be rolled back\. AWS Secrets Manager currently supports two AWS services that enable you to monitor your organization and the activity that happens within it\.

**Topics**
+ [Logging AWS Secrets Manager API Calls with AWS CloudTrail](#monitoring_cloudtrail)
+ [Amazon CloudWatch Events](#monitoring_cloudwatch)

## Logging AWS Secrets Manager API Calls with AWS CloudTrail<a name="monitoring_cloudtrail"></a>

AWS Secrets Manager is integrated with AWS CloudTrail, a service that provides a record of actions taken by a user, role or an AWS service in Secrets Manager\. CloudTrail captures all API calls for Secrets Manager as events, including calls from the Secrets Manager console and from code calls to the Secrets Manager APIs\. If you create a trail, you can enable continuous delivery of CloudTrail events to an Amazon S3 bucket, including events for Secrets Manager\. If you don't configure a trail, you can still view the most recent events in the CloudTrail console in **Event history**\. Using the information collected by CloudTrail, you can determine the request that was made to Secrets Manager the IP address from which the request was made, who made the request, when it was made, and additional details\.

To learn more about CloudTrail see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

### Secrets Manager Information in CloudTrail<a name="service-name-info-in-cloudtrail"></a>

CloudTrail is enabled on your AWS account when you create the account\. When activity occurs in Secrets Manager, that activity is recorded in a CloudTrail event along with other AWS service events in **Event history**\. You can view, search, and download recent events in your AWS account\. For more information, see [Viewing Events with CloudTrail Event History](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html)\.

For an ongoing record of events in your AWS account, including events for Secrets Manager, create a trail\. A trail enables CloudTrail to deliver log files to an Amazon S3 bucket\. By default, when you create a trail in the console, the trail applies to all regions\. The trail logs events from all regions in the AWS partition and delivers the log files to the Amazon S3 bucket that you specify\. Additionally, you can configure other AWS services to further analyze and act upon the event data collected in CloudTrail logs\. For more information, see: 
+ [Overview for Creating a Trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html)
+ [CloudTrail Supported Services and Integrations](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-aws-service-specific-topics.html#cloudtrail-aws-service-specific-topics-integrations)
+ [Configuring Amazon SNS Notifications for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)
+ [Receiving CloudTrail Log Files from Multiple Regions](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/receive-cloudtrail-log-files-from-multiple-regions.html) and [Receiving CloudTrail Log Files from Multiple Accounts](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)

All Secrets Manager actions are logged by CloudTrail and are documented in the [AWS Secrets Manager API Reference](https://docs.aws.amazon.com/secretsmanager/latest/apireference/)\. For example, calls to the `CreateSecret`, `GetSecretValue` and `RotateSecret` sections generate entries in the CloudTrail log files\. 

Every event or log entry contains information about who generated the request\. The identity information helps you determine the following: 
+ Whether the request was made with root or IAM user credentials\.
+ Whether the request was made with temporary security credentials for a role or federated user\.
+ Whether the request was made by another AWS service\.

If you want to be notified when log files are delivered, you can configure CloudTrail to publish Amazon SNS notifications whenever new log files are delivered\. For more information, see [Configuring Amazon SNS Notifications for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)\.

You also can aggregate AWS Secrets Manager log files from multiple AWS Regions and multiple AWS accounts into a single Amazon S3 bucket\. 

For more information, see [Receiving CloudTrail Log Files from Multiple Regions](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html) and [Receiving CloudTrail Log Files from Multiple Accounts](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)\.

### Retrieving Secrets Manager Log File Entries<a name="retrieve-ct-entries"></a>

You can retrieve individual events from CloudTrail by using any of the following techniques:

**To retrieve Secrets Manager events from CloudTrail logs**

------
#### [ Using the AWS Management Console ]

The CloudTrail console enables you to view events that occurred within the past 90 days\.

1. Open the CloudTrail console at [https://console\.aws\.amazon\.com/cloudtrail/](https://console.aws.amazon.com/cloudtrail/)\.

1. Ensure that the console is pointing at the region where your events occurred\. The console shows only those events that occurred in the selected region\. Choose the region from the drop\-down list in the upper\-right corner of the console\.

1. In the left\-hand navigation pane, choose **Event history**\.

1. Choose **Filter** criteria and/or a **Time range** to help you find the event that you're looking for\. For example, to see all Secrets Manager events, for **Select attribute**, choose **Event source**\. Then, for **Enter event source**, choose **secretsmanager\.amazonaws\.com**\.

1. To see additional details, choose the expand arrow next to event you want to view\. To see all of the information available, choose **View event**\.

------
#### [ Using the AWS CLI or SDK Operations ]

1. Open a command window where you can run AWS CLI commands\.

1. Run a command similar to the following example\. The output is shown as word wrapped here for readability, but the real output isn't\.

   ```
   $ aws cloudtrail lookup-events --region us-east-1 --lookup-attributes AttributeKey=EventSource,AttributeValue=secretsmanager.amazonaws.com
   {
       "Events": [
           {
               "EventId": "EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE",
               "EventName": "CreateSecret",
               "EventTime": 1525106994.0,
               "Username": "Administrator",
               "Resources": [],
               "CloudTrailEvent": "{\"eventVersion\":\"1.05\",\"userIdentity\":{\"type\":\"IAMUser\",\"principalId\":\"AKIAIOSFODNN7EXAMPLE\",
                     \"arn\":\"arn:aws:iam::123456789012:user/Administrator\",\"accountId\":\"123456789012\",\"accessKeyId\":\"AKIAIOSFODNN7EXAMPLE\",
                     \"userName\":\"Administrator\"},\"eventTime\":\"2018-04-30T16:49:54Z\",\"eventSource\":\"secretsmanager.amazonaws.com\",
                     \"eventName\":\"CreateSecret\",\"awsRegion\":\"us-east-1\",\"sourceIPAddress\":\"192.168.100.101\",
                     \"userAgent\":\"<useragent string>\",\"requestParameters\":{\"name\":\"MyTestSecret\",
                     \"clientRequestToken\":\"EXAMPLE2-90ab-cdef-fedc-ba987EXAMPLE\"},\"responseElements\":null,
                     \"requestID\":\"EXAMPLE3-90ab-cdef-fedc-ba987EXAMPLE\",\"eventID\":\"EXAMPLE4-90ab-cdef-fedc-ba987EXAMPLE\",
                     \"eventType\":\"AwsApiCall\",\"recipientAccountId\":\"123456789012\"}"
           }
       ]
   }
   ```

------

### Understanding Secrets Manager Log File Entries<a name="understanding-service-name-entries"></a>

A trail is a configuration that enables delivery of events as log files to an Amazon S3 bucket that you specify\. CloudTrail log files contain one or more log entries\. An event represents a single request from any source and includes information about the requested action, the date and time of the action, request parameters, and so on\. CloudTrail log files are not an ordered stack trace of the public API calls, so they do not appear in any specific order\. 

The following example shows a CloudTrail log entry for a sample `CreateSecret` call:

```
  {
    "eventVersion": "1.05",
    "userIdentity": {
      "type": "Root",
      "principalId": "123456789012",
      "arn": "arn:aws:iam::123456789012:root",
      "accountId": "123456789012",
      "accessKeyId": "AKIAIOSFODNN7EXAMPLE",
      "userName": "myusername",
      "sessionContext": {"attributes": {
        "mfaAuthenticated": "false",
        "creationDate": "2018-04-03T17:43:50Z"
      }}
    },
    "eventTime": "2018-04-03T17:50:55Z",
    "eventSource": "secretsmanager.amazonaws.com",
    "eventName": "CreateSecret",
    "awsRegion": "us-west-2",
    "requestParameters": {
      "name": "MyDatabaseSecret",
      "clientRequestToken": "EXAMPLE1-90ab-cdef-fedc-ba987EXAMPLE"
    },
    "responseElements": null,
    "requestID": "EXAMPLE2-90ab-cdef-fedc-ba987EXAMPLE",
    "eventID": "EXAMPLE3-90ab-cdef-fedc-ba987EXAMPLE",
    "eventType": "AwsApiCall",
    "recipientAccountId": "123456789012"
}
```

The following example shows a CloudTrail log entry for a sample `DeleteSecret` call:

```
{
    "eventVersion": "1.05",
    "userIdentity": {
      "type": "Root",
      "principalId": "123456789012",
      "arn": "arn:aws:iam::123456789012:root",
      "accountId": "123456789012",
      "accessKeyId": "AKIAIOSFODNN7EXAMPLE",
      "userName": "myusername",
      "sessionContext": {"attributes": {
        "mfaAuthenticated": "false",
        "creationDate": "2018-04-03T17:43:50Z"
      }}
    },
    "eventTime": "2018-04-03T17:51:02Z",
    "eventSource": "secretsmanager.amazonaws.com",
    "eventName": "DeleteSecret",
    "awsRegion": "us-west-2",
    "requestParameters": {
      "recoveryWindowInDays": 30,
      "secretId": "MyDatabaseSecret"
    },
    "responseElements": {
      "name": "MyDatabaseSecret",
      "deletionDate": "May 3, 2018 5:51:02 PM",
      "aRN": "arn:aws:secretsmanager:us-west-2:123456789012:secret:MyDatabaseSecret-a1b2c3"
    },
    "requestID": "EXAMPLE2-90ab-cdef-fedc-ba987EXAMPLE",
    "eventID": "EXAMPLE3-90ab-cdef-fedc-ba987EXAMPLE",
    "eventType": "AwsApiCall",
    "recipientAccountId": "123456789012"
}
```

## Amazon CloudWatch Events<a name="monitoring_cloudwatch"></a>

Secrets Manager can work with CloudWatch Events to trigger alerts when administrator\-specified operations occur in an organization\. For example, because of the sensitivity of such operations, administrators might want to be warned when a secret is deleted or when a secret is rotated\. Another common need is for an alert if anyone tries to use a secret version while it's in its waiting period to be deleted\. You can configure CloudWatch Events rules that look for these operations and then send the generated events to administrator defined "targets"\. A target could be an Amazon SNS topic that emails or text messages its subscribers\. You can also create a simple AWS Lambda function that's triggered by the event, which logs the details of the operation for your later review\.

To learn more about CloudWatch Events, including how to configure and enable it, see the [Amazon CloudWatch Events User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/)\.

### Monitoring Secret Versions Scheduled for Deletion<a name="monitoring_cloudwatch_deleted-secrets"></a>

You can use a combination of AWS CloudTrail, Amazon CloudWatch Logs, and Amazon Simple Notification Service \(Amazon SNS\) to create an alarm that notifies you of any attempts to access a version of a secret that's pending deletion\. If you receive a notification from such an alarm, you might want to cancel deletion of the secret to give yourself more time to determine whether you really want to delete it\. Your investigation might result in the secret being restored because it really is still needed\. Alternatively, you might need to update the user with details of the new secret that they really should be using\.

The following procedures explain how to receive a notification when a request for the `GetSecretValue` operation that results in a specific error message is written to your CloudTrail log files\. Other API operations can be performed on the version of the secret without triggering the alarm\. The intent of this CloudWatch alarm is to detect usage that might indicate that a person or application is still trying to use credentials that should no longer be used\.

Before you begin these procedures, you must have already turned on CloudTrail in the AWS Region and account where you intend to monitor AWS Secrets ManagerAPI requests\. For instructions, go to [Creating a Trail for the First Time](http://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-a-trail-using-the-console-first-time.html) in the *AWS CloudTrail User Guide*\.

**Topics**
+ [Part 1: Configure CloudTrail Log File Delivery to CloudWatch Logs](#monitoring_cloudwatch_deleted-secrets_part1)
+ [Part 2: Create the CloudWatch Alarm](#monitoring_cloudwatch_deleted-secrets_part2)
+ [](#monitoring_cloudwatch_deleted-secrets_part3)

#### Part 1: Configure CloudTrail Log File Delivery to CloudWatch Logs<a name="monitoring_cloudwatch_deleted-secrets_part1"></a>

You must configure delivery of your CloudTrail log files to CloudWatch Logs\. You do this so that CloudWatch Logs can monitor them for Secrets Manager API requests to retrieve a version of a secret that's pending deletion\.

**To configure CloudTrail log file delivery to CloudWatch Logs**

1. Open the CloudTrail console at [https://console\.aws\.amazon\.com/cloudtrail/](https://console.aws.amazon.com/cloudtrail/)\.

1. On the top navigation bar, choose the AWS Region where you want to monitor secrets\.

1. In the left navigation pane, choose **Trails**, and then choose the name of the trail you want to configure for CloudWatch\.

1. On the **Trails Configuration** page, scroll down to the **CloudWatch Logs** section, and then choose the edit icon \(![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/edit-pencil-icon.png)\)\.

1. For **New or existing log group**, type a name for the log group, such as **CloudTrail/MyCloudWatchLogGroup**\.

1. For **IAM role**, you can use the default role named **CloudTrail\_CloudWatchLogs\_Role**\. That role has a default role policy with the required permissions to deliver CloudTrail events to the log group\.

1. Choose **Continue** to save your configuration\.

1. On the **AWS CloudTrail will deliver CloudTrail events associated with API activity in your account to your CloudWatch Logs log group** page, choose **Allow**\.

#### Part 2: Create the CloudWatch Alarm<a name="monitoring_cloudwatch_deleted-secrets_part2"></a>

To receive a notification when a Secrets Manager `GetSecretValue` API operation requests to access a version of a secret that's pending deletion, you must create a CloudWatch alarm and configure notification\.

**To create a CloudWatch alarm that monitors usage of a version of a secret that's pending deletion**

1. Sign in to the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. On the top navigation bar, choose the AWS Region where you want to monitor secrets\.

1. In the left navigation pane, choose **Logs**\.

1. In the list of **Log Groups**, select the check box next to the log group that you created in the previous procedure, such as **CloudTrail/MyCloudWatchLogGroup**\. Then choose **Create Metric Filter**\.

1. For **Filter Pattern**, type or paste the following:

   ```
   { $.eventName = "GetSecretValue" && $.errorMessage = "*secret because it was deleted*" }
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
      + To use an existing Amazon SNS topic, choose the name of the topic to use\. If a list isn't available, choose **Select list**\.

   1. Choose **Create Alarm**\.

#### <a name="monitoring_cloudwatch_deleted-secrets_part3"></a>

Your alarm is now in place\. To test it, create a version of a secret and then schedule it for deletion\. Then, try to retrieve the secret value\. You'll shortly receive an email at the address that's configured in the alarm\. It will alert you to the use of a secret version that's scheduled for deletion\.