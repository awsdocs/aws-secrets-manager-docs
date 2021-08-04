# Monitoring Secrets Manager secrets using AWS Config<a name="integrating_awsconfig"></a>



AWS Secrets Manager integrates with AWS Config and provides easier tracking of secret changes in Secrets Manager\. You can use two additional managed AWS Config [rules ](https://docs.aws.amazon.com/config/latest/developerguide/evaluate-config.html)for defining your organizational internal guidelines on secrets management best practices\. Also, you can quickly identify secrets that don't conform to your security rules as well as receive notifications from Amazon SNS about your secrets configuration changes\. For example, you can receive Amazon SNS notifications for a list of secrets not configured for rotation which enables you to drive security best practices for rotating secrets\.

When you leverage the AWS Config Multi\-Account Multi\-Region Data Aggregator, you can view a list of secrets and verify conformity across all accounts and regions in your entire organization, and by doing so, create secrets management best practices across your organization from a single location\.<a name="benefits-aws-config"></a><a name="benefits-aws-config.title"></a>

AWS Config allows you to assess, audit, and evaluate configurations across your AWS resources by continually monitoring and recording your AWS resource configurations, and also allows you to automate the evaluation of recorded configurations against desired configurations\. Using AWS Config, you can perform the following tasks: 
+ Review changes in configurations and relationships between AWS resources\.
+ Track detailed resource configuration histories\.
+ Determine your overall compliance with configurations specified in your internal guidelines\.

If you have multi\-account and multi\-region AWS resources in AWS Config, you can view configuration and compliance data from multiple accounts and regions into a single account\. As a result, you can identify noncompliant resources across multiple accounts\.\.

By tracking your secrets with AWS Config, a secret becomes an AWS Config supported resource, and you can track changes to the secret metadata, such as secret description and rotation configuration, relationship to other AWS sources such as the KMS Key used for secret encryption, Lambda function used for secret rotation, as well as attributes such as tags associated with the secrets\. 

You can also leverage [AWS Managed Config Rules](https://docs.aws.amazon.com/config/latest/developerguide/evaluate-config_use-managed-rules.html) to evaluate if your secrets configuration complies with your organization’s security and compliance requirements, and identify secrets that don’t conform to these standards\.

**Topics**
+ [AWS Config supported rules for Secrets Manager](aws-config-rules.md)
+ [Implementing secrets management best practices using AWS Config](implementing-awsconfig-rules.md)
+ [Configuring AWS Config Secrets Manager rules](configuring-awsconfig-rules.md)
+ [Configuring AWS Config Multi\-Account Multi\-Region Data Aggregator for secrets management best practices](configure-awsconfig-aggregator.md)