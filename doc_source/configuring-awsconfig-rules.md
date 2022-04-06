# Audit secrets for compliance by using AWS Config<a name="configuring-awsconfig-rules"></a>

You can use AWS Config to evaluate your secrets and assess how well they comply with your internal practices, industry guidelines, and regulations\. You define your internal security and compliance requirements for secrets using AWS Config rules\. Then AWS Config can identify secrets that don't conform to your rules\. You can also track changes to secret metadata, rotation configuration, the KMS key used for secret encryption, the Lambda rotation function, and tags associated with a secret\.

## <a name="monitoring_CClong"></a>

You can receive notifications from Amazon SNS about your secret configurations\. For example, you can receive Amazon SNS notifications for a list of secrets not configured for rotation which enables you to drive security best practices for rotating secrets\.

If you have secrets in multiple AWS accounts and AWS Regions in your organization, you can aggregate that configuration and compliance data\. 

Monitoring secrets with AWS Config is supported in all AWS Regions except Asia Pacific \(Jakarta\)\.

**To add a new rule for your secrets**
+ Follow the instructions on [Working with AWS Config managed rules](https://docs.aws.amazon.com/config/latest/developerguide/managing-aws-managed-rules.html), and choose one of the following rules:
  + `[secretsmanager\-rotation\-enabled\-check](https://docs.aws.amazon.com/config/latest/developerguide/secretsmanager-rotation-enabled-check.html)` — Checks whether rotation is configured for secrets stored in Secrets Manager\. 
  + `[secretsmanager\-scheduled\-rotation\-success\-check](https://docs.aws.amazon.com/config/latest/developerguide/secretsmanager-scheduled-rotation-success-check.html)`— Checks whether secrets were successfully rotated\. AWS Config also checks if the last rotated date falls within the configured rotation frequency\. 
  + `[secretsmanager\-secret\-periodic\-rotation](https://docs.aws.amazon.com/config/latest/developerguide/secretsmanager-secret-periodic-rotation.html)`— Checks whether secrets were rotated within the specified number of days\.
  + `[secretsmanager\-secret\-unused](https://docs.aws.amazon.com/config/latest/developerguide/secretsmanager-secret-unused.html)`— Checks whether secrets were accessed within the specified number of days\.
  + `[secretsmanager\-using\-cmk](https://docs.aws.amazon.com/config/latest/developerguide/secretsmanager-using-cmk.html)` — Checks whether secrets are encrypted using the AWS managed key `aws/secretsmanager` or a customer managed key you created in AWS KMS\.

After you save the rule, AWS Config evaluates your secrets every time the metadata of a secret changes\. You can configure AWS Config to notify you of changes\. For more information, see [ Notifications that AWS Config sends to an Amazon SNS topic](https://docs.aws.amazon.com/config/latest/developerguide/notifications-for-AWS-Config.html)\.

## Aggregate secrets from your AWS accounts and AWS Regions<a name="configure-awsconfig-aggregator"></a>

You can configure AWS Config Multi\-Account Multi\-Region Data Aggregator to review configurations of your secrets across all accounts and regions in your organization, and then review your secret configurations and compare to secrets management best practices\. 

You must enable AWS Config and the AWS Config managed rules specific to secrets across all accounts and regions before you create an aggregator\. For more information, see [Use CloudFormation StackSets to provision resources across multiple AWS accounts and Regions](http://aws.amazon.com/blogs/aws/use-cloudformation-stacksets-to-provision-resources-across-multiple-aws-accounts-and-regions)\.

For more information about AWS Config Aggregator, see [Multi\-Account Multi\-Region Data Aggregation](https://docs.aws.amazon.com/config/latest/developerguide/aggregate-data.html) and [Setting Up an Aggregator Using the Console](https://docs.aws.amazon.com/config/latest/developerguide/setup-aggregator-console.html) in the AWS Config Developer Guide\. 