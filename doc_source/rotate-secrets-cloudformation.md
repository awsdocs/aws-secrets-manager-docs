# Turn on rotation for Secrets Manager secrets in AWS CloudFormation<a name="rotate-secrets-cloudformation"></a>

To turn on rotation in a AWS CloudFormation template, you use the following CloudFormation resources:
+ [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-secret.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-secret.html)
+ [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-secrettargetattachment.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-secrettargetattachment.html)
+ [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-rotationschedule.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-rotationschedule.html)

You also use the [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/transform-aws-secretsmanager.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/transform-aws-secretsmanager.html)\.

Examples:
+ [Create a secret with Amazon RDS credentials with automatic rotation](cfn-example_RDSsecret.md)
+ [Create a secret with Amazon Redshift credentials with automatic rotation](cfn-example_Redshift-secret.md)
+ [Create a secret with Amazon DocumentDB credentials with automatic rotation](cfn-example_DocDB-secret.md)