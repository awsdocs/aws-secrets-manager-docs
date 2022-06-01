# Logging AWS Secrets Manager events with AWS CloudTrail<a name="retrieve-ct-entries"></a>

AWS CloudTrail records all [API calls for Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_Operations.html) as events, including calls from the Secrets Manager console\. CloudTrail also captures the following events:
+ `RotationAbandoned` event \- Secrets Manager removed the AWSPENDING label from an existing version of a secret\. When you manually create a new version of a secret, you send a message signalling the abandonment of the current ongoing rotation in favor of the new secret version\. As a result, Secrets Manager removes the AWSPENDING label to allow future rotations to succeed and publish a CloudTrail event to provide awareness of the change\. 
+ `RotationStarted` event \- A secret started rotation\. 
+ `RotationSucceeded` event \- A successful rotation event\.
+ `RotationFailed` event \- Secret rotation failed\. 
+ `StartSecretVersionDelete` event \- a mechanism that notifies you of the start deletion for a secret version\.
+ `CancelSecretVersionDelete` event \- A delete cancellation for a secret version\. 
+ `EndSecretVersionDelete` event \- An ending secret version deletion\.

You can use the CloudTrail console to view the last 90 days of recorded events\. For an ongoing record of events in your AWS account, including events for Secrets Manager, create a trail so that CloudTrail delivers log files to an Amazon S3 bucket\. See [Creating a trail for your AWS account](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html)\. You can also configure CloudTrail to receive CloudTrail log files from [multiple AWS accounts](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html) and [AWS Regions](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/receive-cloudtrail-log-files-from-multiple-regions.html)\.

You can configure other AWS services to further analyze and act upon the data collected in CloudTrail logs\. See [AWS service integrations with CloudTrail logs](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-aws-service-specific-topics.html#cloudtrail-aws-service-specific-topics-integrations)\. You can also get notifications when CloudTrail publishes new log files to your Amazon S3 bucket\. See [Configuring Amazon SNS notifications for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)\.

**To retrieve Secrets Manager events from CloudTrail logs \(console\)**

1. Open the CloudTrail console at [https://console\.aws\.amazon\.com/cloudtrail/](https://console.aws.amazon.com/cloudtrail/)\.

1. Ensure that the console points to the region where your events occurred\. The console shows only those events that occurred in the selected region\. Choose the region from the drop\-down list in the upper\-right corner of the console\.

1. In the left\-hand navigation pane, choose **Event history**\.

1. Choose **Filter** criteria and/or a **Time range** to help you find the event that you're looking for\. For example, to see all Secrets Manager events, for **Select attribute**, choose **Event source**\. Then, for **Enter event source**, choose **secretsmanager\.amazonaws\.com**\.

1. To see additional details, choose the expand arrow next to event\. To see all of the information available, choose **View event**\.

## AWS CLI or SDK<a name="w400aac23b7c13"></a>

**To retrieve Secrets Manager events from CloudTrail logs \(AWS CLI or SDK\)**

1. Open a command window to run AWS CLI commands\.

1. Run a command similar to the following example\. 

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
                     \"eventName\":\"CreateSecret\",\"awsRegion\":\"us-east-2\",\"sourceIPAddress\":\"192.168.100.101\",
                     \"userAgent\":\"<useragent string>\",\"requestParameters\":{\"name\":\"MyTestSecret\",
                     \"clientRequestToken\":\"EXAMPLE2-90ab-cdef-fedc-ba987EXAMPLE\"},\"responseElements\":null,
                     \"requestID\":\"EXAMPLE3-90ab-cdef-fedc-ba987EXAMPLE\",\"eventID\":\"EXAMPLE4-90ab-cdef-fedc-ba987EXAMPLE\",
                     \"eventType\":\"AwsApiCall\",\"recipientAccountId\":\"123456789012\"}"
           }
       ]
   }
   ```

## Examples of Secrets Manager log entries<a name="understanding-service-name-entries"></a>

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
    "awsRegion": "us-east-2",
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
    "awsRegion": "us-east-2",
    "requestParameters": {
      "recoveryWindowInDays": 30,
      "secretId": "MyDatabaseSecret"
    },
    "responseElements": {
      "name": "MyDatabaseSecret",
      "deletionDate": "May 3, 2018 5:51:02 PM",
      "aRN": "arn:aws:secretsmanager:us-east-2:123456789012:secret:MyDatabaseSecret-a1b2c3"
    },
    "requestID": "EXAMPLE2-90ab-cdef-fedc-ba987EXAMPLE",
    "eventID": "EXAMPLE3-90ab-cdef-fedc-ba987EXAMPLE",
    "eventType": "AwsApiCall",
    "recipientAccountId": "123456789012"
}
```