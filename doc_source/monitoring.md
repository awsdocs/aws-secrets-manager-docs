# Monitor AWS Secrets Manager secrets<a name="monitoring"></a>

We recommend that you monitor your secrets and log changes to them\. This helps you to ensure that any unexpected usage or change can be investigated, and unwanted changes can be rolled back\. 

## Use AWS CloudTrail to log activity for your secrets<a name="monitoring_CTlong"></a>

AWS CloudTrail records all [API calls for Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_Operations.html) as events, including calls from the Secrets Manager console\. CloudTrail also logs a number of non\-API events to help you track when rotation starts, succeeds, or is abandoned, as well as when secrets are schedule for deletion\. See [View CloudTrail log file entries for Secrets Manager](retrieve-ct-entries.md)\.

You can use the CloudTrail console to view the last 90 days of recorded events\. For an ongoing record of events in your AWS account, including events for Secrets Manager, create a trail so that CloudTrail delivers log files to an Amazon S3 bucket\. See [Creating a trail for your AWS account](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html)\. You can also configure CloudTrail to receive CloudTrail log files from [multiple AWS accounts](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html) and [ AWS Regions](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/receive-cloudtrail-log-files-from-multiple-regions.html)\.

You can configure other AWS services to further analyze and act upon the data collected in CloudTrail logs\. See [AWS service integrations with CloudTrail logs](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-aws-service-specific-topics.html#cloudtrail-aws-service-specific-topics-integrations)\. You can also get notifications when CloudTrail publishes new log files to your Amazon S3 bucket\. See [Configuring Amazon SNS notifications for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)\.

## Use Amazon CloudWatch to respond when events of interest occur<a name="monitoring_CWlong"></a>

For example, a text message can alert you whenever someone creates a new secret, or when a secret rotates successfully\. You could also create an alert for when a client attempts to use a deprecated version of a secret instead of the current version\. This can help with troubleshooting\.

Secrets Manager can work with CloudWatch Events to trigger alerts when administrator\-specified operations occur in an organization\. For example, because of the sensitivity of such operations, administrators might want to be warned of deleted secrets or secret rotation\. You might want an alert if anyone tries to use a secret version in the waiting period to be deleted\. You can configure CloudWatch Events rules that look for these operations and then send the generated events to administrator defined "targets"\. A target could be an Amazon SNS topic that emails or text messages to subscribers\. You can also create a simple AWS Lambda function triggered by the event, which logs the details of the operation for your later review\.

You can use CloudWatch to monitor estimated Secrets Manager charges\. For more information, see [Creating a billing alarm to monitor your estimated AWS charges](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/monitor_estimated_charges_with_cloudwatch.html)\.

To learn more about CloudWatch Events, including how to configure and enable it, see the [Amazon CloudWatch Events User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/)\.

## Use AWS Config to assess secrets and track changes to them<a name="monitoring_CClong"></a>

AWS Secrets Manager integrates with AWS Config and provides easier tracking of secret changes in Secrets Manager\. You define your internal security and compliance requirements for secrets using AWS Config rules\. Then AWS Config can identify secrets that don't conform to your rules\. You can also track changes to secret metadata, rotation configuration, the KMS key used for secret encryption, the Lambda rotation function, and tags associated with a secret\. See [Audit secrets for compliance by using AWS Config](configuring-awsconfig-rules.md)\.

You can receive notifications from Amazon SNS about your secret configurations\. For example, you can receive Amazon SNS notifications for a list of secrets not configured for rotation which enables you to drive security best practices for rotating secrets\.

If you have secrets in multiple AWS accounts and AWS Regions in your organization, you can aggregate that configuration and compliance data\. 

Monitoring secrets with AWS Config is supported in all AWS Regions except Asia Pacific \(Jakarta\)\.

## Use Security Hub for security best practices in Secrets Manager<a name="integrating-securityhub"></a>

AWS Security Hub provides you with a comprehensive view of your security state in AWS and helps you check your environment against security industry standards and best practices\.

Security Hub collects security data from across AWS accounts, services, and supported third\-party partner products and helps you analyze your security trends and identify the highest priority security issues\. 

The AWS Foundational Security Best Practices standard is a set of controls that detects when your deployed accounts and resources deviate from security best practices\. Security Hub provides a set of controls for Secrets Manager that allows you to continuously evaluate and identify areas of deviation from best practices\. For more information, see [AWS Foundational Security Best Practices controls](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-standards-fsbp-controls.html#fsbp-secretsmanager-1)\.