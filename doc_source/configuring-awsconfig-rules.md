# Audit secrets for compliance by using AWS Config<a name="configuring-awsconfig-rules"></a>

You can use AWS Config to evaluate your secrets and assess how well they comply with your internal practices, industry guidelines, and regulations\. 

**To add a new rule for your secrets**

1. Log into the AWS Config console at [https://console\.aws\.amazon\.com/config/](https://console.aws.amazon.com/config/)\.

1. If you are using the AWS Config console for the first time, see [Setting up AWS Config with the console](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html)\.

1. Choose **Rules**\.

1. In the **Rules** section, choose **Add Rule**\.

1. On the **Specify rule type** page, do the following:

   1. For **Select rule type**, choose **Add AWS managed rule**\.

   1. Choose one of the following rules, and then choose **Next**\.
      + `[secretsmanager\-rotation\-enabled\-check](https://docs.aws.amazon.com/config/latest/developerguide/secretsmanager-rotation-enabled-check.html)` — Checks if you configured rotation for secrets stored in Secrets Manager\. AWS Config verifies you configured the secrets for rotation\. This rule also supports the `maximumAllowedRotationFrequency`, which if specified, compares the frequency configuration of the secret to the value set in the parameter\.
      + `[secretsmanager\-scheduled\-rotation\-success\-check](https://docs.aws.amazon.com/config/latest/developerguide/secretsmanager-scheduled-rotation-success-check.html)`— Checks if Secrets Manager successfully rotates secrets\. AWS Config verifies the rule and checks if the last rotated date falls within the configured rotation frequency\. 
      + `[secretsmanager\-secret\-periodic\-rotation](https://docs.aws.amazon.com/config/latest/developerguide/secretsmanager-secret-periodic-rotation.html)`— Checks if AWS rotated secrets within the past specified number of days\.
      + `[secretsmanager\-secret\-unused](https://docs.aws.amazon.com/config/latest/developerguide/secretsmanager-secret-unused.html)`— Checks if Secrets Manager accessed secrets within a specified number of days\.
      + `[secretsmanager\-using\-cmk](https://docs.aws.amazon.com/config/latest/developerguide/secretsmanager-using-cmk.html)` — Checks if AWS encrypts all secrets using the AWS managed key `aws/secretsmanager` or a customer managed key you created in AWS KMS\.

1. Keep all other fields, and **Save** the rule\.

Once you save the rule, AWS Config evaluates your secrets every time the metadata of a secret changes\. If changes occur, you receive an Amazon SNS notification about noncompliant secrets\. You can also view the results from the **Rules** or **Dashboard** of AWS Config\.

After set up, AWS Config evaluates your AWS Secrets Manager resources against your selected rules\. You can update the rules and create additional managed rules after set up\. 

## Aggregate secrets from your AWS accounts and AWS Regions<a name="configure-awsconfig-aggregator"></a>

You configure AWS Config Multi\-Account Multi\-Region Data Aggregator to review configurations of your secrets across all accounts and regions in your organization, and then review your secret configurations and compare to secrets management best practices\. 

You must enable AWS Config and the AWS Config managed rules specific to secrets across all accounts and regions before you create an aggregator\. For more information, see [Use CloudFormation StackSets to provision resources across multiple AWS accounts and Regions](http://aws.amazon.com/blogs/aws/use-cloudformation-stacksets-to-provision-resources-across-multiple-aws-accounts-and-regions)\.

For more information about AWS Config Aggregator, see [Multi\-Account Multi\-Region Data Aggregation](https://docs.aws.amazon.com/config/latest/developerguide/aggregate-data.html) in the AWS Config Developer Guide\.

You can create an aggregator from your organization's management account or from any member account in your organization\. 

**To aggregate secrets from your AWS accounts and AWS Regions**

1. Log into the AWS Config console at [https://console\.aws\.amazon\.com/config/](https://console.aws.amazon.com/config/)\.

1. Choose **Aggregators** and then **Add Aggregator**\.

1. In the **Allow data replication** section, check the **Allow AWS Config to replicate data**\. You must enable this to provide the management account access to the resource configuration and compliance details for all of the accounts and regions in your organization\.

1. Type a unique name, consisting of up to 64 alphanumeric characters, for your aggregator in the **Aggregator name** field\.

1. In the **Select source accounts** section, choose **Add my organization**, and then click **Choose IAM role**\. 

1. Under **Regions**, select all regions applicable to your organization and then choose **Save**\.

To view a dashboard of all secrets in your organization, choose the name of your aggregator\. You can now see all of the secrets across all accounts and regions\.

For more information on configuring AWS Config aggregators, see [Setting Up an Aggregator Using the Console\. ](https://docs.aws.amazon.com/config/latest/developerguide/setup-aggregator-console.html)

Review the data to identify all noncompliant secrets in your organization\. Work with individual account and secret owners to apply secrets management best practices such as ensuring rotation for all secrets\. Ensure all secrets meet your best practices policies for rotation frequency and you proactively identify unused secrets and schedule them for deletion\.