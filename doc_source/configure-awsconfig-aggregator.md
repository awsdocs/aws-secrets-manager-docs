# Configuring AWS Config Multi\-Account Multi\-Region Data Aggregator for Secrets Management Best Practices<a name="configure-awsconfig-aggregator"></a>

You configure AWS Config Multi\-Account Multi\-Region Data Aggregator to review configurations of your secrets across all accounts and regions in your organization, and then review your secret configurations and compare to secrets management best practices\. 

**Note**  
You must enable AWS Config and the AWS Config managed rules specific to secrets across all accounts and regions before you create an aggregator\. You can use [ CloudFormation StackSets](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-concepts.html) to enable AWS Config and provision rules across all accounts and regions [here](https://docs.aws.amazon.com/blogs/aws/use-cloudformation-stacksets-to-provision-resources-across-multiple-aws-accounts-and-regions/)\.  
For more information about AWS Config Aggregator, see [Multi\-Account Multi\-Region Data Aggregation](https://docs.aws.amazon.com/config/latest/developerguide/aggregate-data.html) in the AWS Config Developer Guide\.

You create an Aggregator from your organization's master account or from any member account in your organization\. Use the following steps to create an AWS Config Aggregator:

1. Log into the AWS Config console at [https://console\.aws\.amazon\.com/config/](https://console.aws.amazon.com/config/)\.

1. Choose **Aggregators** and then **Add Aggregator**\.

1. In the **Allow data replication** section, check the **Allow AWS Config to replicate data**\. You must enable this to provide the master account access to the resource configuration and compliance details for all of the accounts and regions in your organization\.

1. Type a unique name, consisting of up to 64 alphanumeric characters, for your aggregator in the **Aggregator name** field\.

1. In the **Select source accounts** section, choose **Add my organization**, and then click **Choose IAM role**\. 

1. Under **Regions**, select all regions applicable to your organization and then choose **Save**\.

To view a dashboard of all secrets in your organization, choose the name of your aggregator\. You can now see all of the secrets across all accounts and regions\.

For more information on configuring AWS Config aggregators, see [Setting Up an Aggregator Using the Console\. ](https://docs.aws.amazon.com/config/latest/developerguide/setup-aggregator-console.html)

Review the data to identify all noncompliant secrets in your organization\. Work with individual account and secret owners to apply secrets management best practices such as ensuring rotation for all secrets\. Ensure all secrets meet your best practices policies for rotation frequency and you proactively identify unused secrets and schedule them for deletion\.

**Tip**  
More information about Secrets Manager and AWS Config can be found in the following AWS Config documentation:  
[List of AWS Config Managed Rules](https://docs.aws.amazon.com/config/latest/developerguide/managed-rules-by-aws-config.html)
[AWS Config Supported AWS Resource Types and Resource Relationships](https://docs.aws.amazon.com/config/latest/developerguide/resource-config-reference.html)
[Notifications that AWS Config Sends to an Amazon SNS topic](https://docs.aws.amazon.com/config/latest/developerguide/notifications-for-AWS-Config.html)