# Benefits of Tracking Secrets with AWS Config<a name="benefits-aws-config"></a>

AWS Config allows you to assess, audit, and evaluate configurations across your AWS resources by continually monitoring and recording your AWS resource configurations, and also allows you to automate the evaluation of recorded configurations against desired configurations\. Using AWS Config, you can perform the following tasks: 
+ Review changes in configurations and relationships between AWS resources\.
+ Track detailed resource configuration histories\.
+ Determine your overall compliance with configurations specified in your internal guidelines\.

If you have multi\-account and multi\-region AWS resources in AWS Config, you can view configuration and compliance data from multiple accounts and regions into a single account\. As a result, you can identify noncompliant resources across multiple accounts\.\.

By tracking your secrets with AWS Config, a secret becomes an AWS Config supported resource, and you can track changes to the secret metadata, such as secret description and rotation configuration, relationship to other AWS sources such as the KMS Key used for secret encryption, Lambda function used for secret rotation, as well as attributes such as tags associated with the secrets\. 

You can also leverage [AWS Managed Config Rules](https://docs.aws.amazon.com/config/latest/developerguide/evaluate-config_use-managed-rules.html) to evaluate if your secrets configuration complies with your organization’s security and compliance requirements, and identify secrets that don’t conform to these standards\.

**Topics**
+ [AWS Config Supported Rules for Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/aws-config-rules.html)
+ [Implementing Secrets Management Best Practices Using AWS Config](https://docs.aws.amazon.com/secretsmanager/latest/userguide/implementing-awsconfig-rules.html)
+ [Configuring AWS Config Aggregator for Secrets Management Best Practices](secretsmanager/latest/userguide/configure-awsconfig-aggregator.html)