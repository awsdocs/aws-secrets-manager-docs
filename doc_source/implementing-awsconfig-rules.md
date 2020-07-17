# Implementing Secrets Management Best Practices Using AWS Config<a name="implementing-awsconfig-rules"></a>

Secrets Manager rotates secrets as part of a [best practice](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#use-roles-with-ec2) security plan\. Using the managed rule, `secretsmanager-rotation-enabled-check`, AWS Config verifies rotation of secrets in Secrets Manager\.

For more information about rule configuration in AWS Config, see [AWS Config Rules](https://docs.aws.amazon.com/config/latest/developerguide/evaluate-config.html) in the AWS Config Developer Guide\. 