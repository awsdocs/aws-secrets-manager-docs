# Automate secret creation in AWS CloudFormation<a name="integrating_cloudformation"></a>

You can use AWS CloudFormation to create and reference secrets from within your AWS CloudFormation stack template\. You can create a secret and then reference it from another part of the template\. For example, you can retrieve the user name and password from the new secret and then use that to define the user name and password for a new database\. You can create and attach resource\-based policies to a secret\. You can also configure rotation by defining a Lambda function in your template and associating the function with your new secret as its rotation Lambda function\. 

You create an AWS CloudFormation template in either JSON or YAML\. AWS CloudFormation processes the template and builds the resources that are defined in the template\. You can use templates to create a new copy of your infrastructure whenever you need it\. For example, you can duplicate your test infrastructure to create the public version\. You can also share the infrastructure as a simple text file, so other people can replicate the resources\.

Secrets Manager provides the following resource types that you can use to create secrets in an AWS CloudFormation template:
+ **[ AWS::SecretsManager::Secret ](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-secret.html)** – Creates a secret and stores it in Secrets Manager\. You can specify a password or Secrets Manager can generate one for you\. You can also create an empty secret and then update it later using the parameter `SecretString.` 
+ **[ AWS::SecretsManager::ResourcePolicy ](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-resourcepolicy.html)** – Creates a resource\-based policy and attaches it to the secret\. A resource\-based policy controls who can perform actions on the secret\.
+ **[ AWS::SecretsManager::RotationSchedule ](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-rotationschedule.html)** – Configures a secret to perform automatic periodic rotation using the specified Lambda rotation function\.
+ **[ AWS::SecretsManager::SecretTargetAttachment ](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-secrettargetattachment.html)** – Configures the secret with the details about the service or database that Secrets Manager needs to rotate the secret\. For example, for an Amazon RDS DB instance, Secrets Manager adds the connection details and database engine type as entries in the `SecureString` property of the secret\.

For information about creating resources with AWS CloudFormation, see [Learn template basics](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/gettingstarted.templatebasics.html) in the AWS CloudFormation User Guide\.

You can also use the AWS Cloud Development Kit \(CDK\)\. For more information, see [AWS Secrets Manager Construct Library](https://docs.aws.amazon.com/cdk/api/latest/docs/aws-secretsmanager-readme.html)\.

**Topics**
+ [Create a simple secret](cfn-example_secret.md)
+ [Create a secret and an Amazon RDS MySQL DB instance](cfn-example_RDSsecret.md)