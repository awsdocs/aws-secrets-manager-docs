# Monitoring Secrets Manager secrets using AWS Config<a name="integrating_awsconfig"></a>



AWS Secrets Manager integrates with AWS Config and provides easier tracking of secret changes in Secrets Manager\. You can use two additional managed AWS Config [rules ](https://docs.aws.amazon.com/config/latest/developerguide/evaluate-config.html)for defining your organizational internal guidelines on secrets management best practices\. Also, you can quickly identify secrets that don't conform to your security rules as well as receive notifications from Amazon SNS about your secrets configuration changes\. For example, you can receive Amazon SNS notifications for a list of secrets not configured for rotation which enables you to drive security best practices for rotating secrets\.

When you leverage the AWS Config Multi\-Account Multi\-Region Data Aggregator, you can view a list of secrets and verify conformity across all accounts and regions in your entire organization, and by doing so, create secrets management best practices across your organization from a single location\.

**Topics**
+ [Benefits of tracking secrets with AWS Config](benefits-aws-config.md)
+ [AWS Config supported rules for Secrets Manager ](aws-config-rules.md)
+ [Implementing secrets management best practices using AWS Config](implementing-awsconfig-rules.md)
+ [Configuring AWS Config Multi\-Account Multi\-Region Data Aggregator for secrets management best practices](configure-awsconfig-aggregator.md) 