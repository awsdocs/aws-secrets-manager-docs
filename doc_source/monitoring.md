# Monitoring the Use of Your AWS Secrets Manager Secrets<a name="monitoring"></a>

As a best practice, you should monitor your secrets to ensure usage of your secrets and log any changes to them\. This helps you to ensure that any unexpected usage or change can be investigated, and unwanted changes can be rolled back\. AWS Secrets Manager currently supports two AWS services that enable you to monitor your organization and activity\.

**Topics**
+ [Logging AWS Secrets Manager API Calls with AWS CloudTrail](#monitoring_cloudtrail)
+ [Amazon CloudWatch Events](#monitoring_cloudwatch)

## Logging AWS Secrets Manager API Calls with AWS CloudTrail<a name="monitoring_cloudtrail"></a>

AWS Secrets Manager integrates with AWS CloudTrail, a service that provides a record of actions taken by a user, role or an AWS service in Secrets Manager\. CloudTrail captures all API calls for Secrets Manager as events, including calls from the Secrets Manager console and from code calls to the Secrets Manager APIs\. If you create a trail, you can enable continuous delivery of CloudTrail events to an Amazon S3 bucket, including events for Secrets Manager\. If you don't configure a trail, you can still view the most recent events in the CloudTrail console in **Event history**\. Using the information collected by CloudTrail, you can determine the request sent to Secrets Manager, the IP address of the request, who sent the request, when the time of the request, and additional details\.

To learn more about CloudTrail see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

### Logging AWS Secrets Manager non\-API Events<a name="logging-non-API-events"></a>

In addition to logging AWS API calls, CloudTrail captures other related events that might have a security or compliance impact on your AWS account or might help you troubleshoot operational problems\. CloudTrail records these events as non\-API service events\.

Secrets Manager has three non\-API service events:
+ `RotationAbandoned` event \- a mechanism to inform you that the Secrets Manager service removed the AWSPENDING label from an existing version of a secret\.  When you manually create a new version of a secret, you send a message signalling the abandonment of the current ongoing rotation in favor of the new secret version\.  As a result, Secrets Manager removes the AWSPENDING label to allow future rotations to succeed and publish a CloudTrail event to provide awareness of the change\. 
+ `RotationStarted` event \- a mechanism that notifies you of a secret starting rotation\. 
+ `RotationSucceeded` event \- a mechanism that notifies you of a successful rotation event\.
+ `RotationFailed` event \- a mechanism to inform you that secret rotation failed for an application\. 

### Secrets Manager Information in CloudTrail<a name="service-name-info-in-cloudtrail"></a>

When you create your AWS account, AWS enables CloudTrail\. When activity occurs in Secrets Manager, CloudTrail records the activity in a CloudTrail event along with other AWS service events in **Event history**\. You can view, search, and download recent events in your AWS account\. For more information, see [Viewing Events with CloudTrail Event History](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html)\.

For an ongoing record of events in your AWS account, including events for Secrets Manager, create a trail\. A trail enables CloudTrail to deliver log files to an Amazon S3 bucket\. By default, when you create a trail in the console, the trail applies to all regions\. The trail logs events from all regions in the AWS partition and delivers the log files to the Amazon S3 bucket you specify\. Additionally, you can configure other AWS services to further analyze and act upon the event data collected in CloudTrail logs\. For more information, see: 
+ [Overview for Creating a Trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html)
+ [CloudTrail Supported Services and Integrations](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-aws-service-specific-topics.html#cloudtrail-aws-service-specific-topics-integrations)
+ [Configuring Amazon SNS Notifications for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)
+ [Receiving CloudTrail Log Files from Multiple Regions](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/receive-cloudtrail-log-files-from-multiple-regions.html) and [Receiving CloudTrail Log Files from Multiple Accounts](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)

CloudTrail logs all Secrets Manager actions and documents the actions in the [AWS Secrets Manager API Reference](https://docs.aws.amazon.com/secretsmanager/latest/apireference/)\. For example, calls to the `CreateSecret`, `GetSecretValue` and `RotateSecret` sections generate entries in the CloudTrail log files\. 

Every event or log entry contains information about who generated the request\. The identity information helps you determine the following: 
+ If root or IAM user credentials generated the request\.
+ If temporary security credentials for a role or federated user generated the request\.
+ If another AWS service generated the request\.

For notification of log file delivery, you can configure CloudTrail to publish Amazon SNS notifications\. For more information, see [Configuring Amazon SNS Notifications for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)\.

You also can aggregate AWS Secrets Manager log files from multiple AWS Regions and multiple AWS accounts into a single Amazon S3 bucket\. 

For more information, see [Receiving CloudTrail Log Files from Multiple Regions](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html) and [Receiving CloudTrail Log Files from Multiple Accounts](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)\.

### Retrieving Secrets Manager Log File Entries<a name="retrieve-ct-entries"></a>

You can retrieve individual events from CloudTrail by using any of the following techniques:

**To retrieve Secrets Manager events from CloudTrail logs**

------
#### [ Using the AWS Management Console ]

The CloudTrail console enables you to view events that occurred within the past 90 days\.

1. Open the CloudTrail console at [https://console\.aws\.amazon\.com/cloudtrail/](https://console.aws.amazon.com/cloudtrail/)\.

1. Ensure that the console points to the region where your events occurred\. The console shows only those events that occurred in the selected region\. Choose the region from the drop\-down list in the upper\-right corner of the console\.

1. In the left\-hand navigation pane, choose **Event history**\.

1. Choose **Filter** criteria and/or a **Time range** to help you find the event that you're looking for\. For example, to see all Secrets Manager events, for **Select attribute**, choose **Event source**\. Then, for **Enter event source**, choose **secretsmanager\.amazonaws\.com**\.

1. To see additional details, choose the expand arrow next to event\. To see all of the information available, choose **View event**\.

------
#### [ Using the AWS CLI or SDK Operations ]

1. Open a command window to run AWS CLI commands\.

1. Run a command similar to the following example\. For readability here, the output displays as word\-wrapped, but the real output doesn't\.

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

A trail enables delivery of events as log files to a specified Amazon S3 bucket\. CloudTrail log files contain one or more log entries\. An event represents a single request from any source and includes information about the requested action, the date and time of the action, request parameters, and so on\. CloudTrail log files does not collect an ordered stack trace of the public API calls, so they do not appear in any specific order\. 

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

Secrets Manager can work with CloudWatch Events to trigger alerts when administrator\-specified operations occur in an organization\. For example, because of the sensitivity of such operations, administrators might want to be warned of deleted secrets or secret rotation\. You might want an alert if anyone tries to use a secret version in the waiting period to be deleted\. You can configure CloudWatch Events rules that look for these operations and then send the generated events to administrator defined "targets"\. A target could be an Amazon SNS topic that emails or text messages to subscribers\. You can also create a simple AWS Lambda function triggered by the event, which logs the details of the operation for your later review\.

To learn more about CloudWatch Events, including how to configure and enable it, see the [Amazon CloudWatch Events User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/)\.

### Monitoring Secret Versions Scheduled for Deletion<a name="monitoring_cloudwatch_deleted-secrets"></a>

You can use a combination of AWS CloudTrail, Amazon CloudWatch Logs, and Amazon Simple Notification Service \(Amazon SNS\) to create an alarm that notifies you of any attempts to access a version of a secret pending deletion\. If you receive a notification from an alarm, you might want to cancel deletion of the secret to give yourself more time to determine if you really want to delete it\. Your investigation might result in the secret being restored because you still need the secret\. Alternatively, you might need to update the user with details of the new secret to use\.

The following procedures explain how to receive a notification when a request for the `GetSecretValue` operation that results in a specific error message written to your CloudTrail log files\. Other API operations can be performed on the version of the secret without triggering the alarm\. This CloudWatch alarm detects usage that might indicate a person or application using outdated credentials\.

Before you begin these procedures, you must turn on CloudTrail in the AWS Region and account where you intend to monitor AWS Secrets ManagerAPI requests\. For instructions, go to [Creating a Trail for the First Time](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-a-trail-using-the-console-first-time.html) in the *AWS CloudTrail User Guide*\.

**Topics**
+ [Part 1: Configuring CloudTrail Log File Delivery to CloudWatch Logs](#monitoring_cloudwatch_deleted-secrets_part1)
+ [Part 2: Create the CloudWatch Alarm](#monitoring_cloudwatch_deleted-secrets_part2)
+ [Part 3: Monitoring CloudWatch for Deleted Secrets](#monitoring_cloudwatch_deleted-secrets_part3)

#### Part 1: Configuring CloudTrail Log File Delivery to CloudWatch Logs<a name="monitoring_cloudwatch_deleted-secrets_part1"></a>

You must configure delivery of your CloudTrail log files to CloudWatch Logs\. You do this so CloudWatch Logs can monitor them for Secrets Manager API requests to retrieve a version of a secret pending deletion\.

**To configure CloudTrail log file delivery to CloudWatch Logs**

1. Open the CloudTrail console at [https://console\.aws\.amazon\.com/cloudtrail/](https://console.aws.amazon.com/cloudtrail/)\.

1. On the top navigation bar, choose the AWS Region to monitor secrets\.

1. In the left navigation pane, choose **Trails**, and then choose the name of the trail to configure for CloudWatch\.

1. On the **Trails Configuration** page, scroll down to the **CloudWatch Logs** section, and then choose the edit icon \(![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/edit-pencil-icon.png)\)\.

1. For **New or existing log group**, type a name for the log group, such as **CloudTrail/MyCloudWatchLogGroup**\.

1. For **IAM role**, you can use the default role named **CloudTrail\_CloudWatchLogs\_Role**\. This role has a default role policy with the required permissions to deliver CloudTrail events to the log group\.

1. Choose **Continue** to save your configuration\.

1. On the **AWS CloudTrail will deliver CloudTrail events associated with API activity in your account to your CloudWatch Logs log group** page, choose **Allow**\.

#### Part 2: Create the CloudWatch Alarm<a name="monitoring_cloudwatch_deleted-secrets_part2"></a>

To receive a notification when a Secrets Manager `GetSecretValue` API operation requests to access a version of a secret pending deletion, you must create a CloudWatch alarm and configure notification\.

**Creating a CloudWatch alarm to monitors usage of a version of a secret pending deletion**

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

#### Part 3: Monitoring CloudWatch for Deleted Secrets<a name="monitoring_cloudwatch_deleted-secrets_part3"></a>

You have created your alarm\. To test it, create a version of a secret and then schedule it for deletion\. Then, try to retrieve the secret value\. You shortly receive an email at the address configured in the alarm\. It alerts you to the use of a secret version scheduled for deletion\.