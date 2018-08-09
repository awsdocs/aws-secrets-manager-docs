# Document History for AWS Secrets Manager<a name="document-history"></a>

The following table describes major documentation updates for AWS Secrets Manager\.
+ **API version: 2017\-10\-17**

| Change | Description | Date | 
| --- |--- |--- |
| [Delete a secret without a recovery window](http://docs.aws.amazon.com/secretsmanager/latest/userguide/rotation-network-rqmts.html) | You can now delete secrets without specifying a recovery window\. This enables you to 'clean up' unneeded secrets without having to wait a minimum of seven days\. | August 9, 2018 | 
| [Private VPC service endpoints](http://docs.aws.amazon.com/secretsmanager/latest/userguide/rotation-network-rqmts.html) | You can now configure private service endpoints for Secrets Manager within your VPCs\. This enables you to call Secrets Manager API operations from within a VPC without requiring connection to the public internet\. | July 11, 2018 | 
| [Resource\-based policies](http://docs.aws.amazon.com/secretsmanager/latest/userguide/auth-and-access_resource-based-policies.html) | You can now attach IAM permission policies directly to a secret to determine who can access that secret\. This also enables cross\-account access because you can specify other AWS accounts in the `Principal` element of a resource\-based policy\. | June 26, 2018 | 
| [Compliance with HIPAA](http://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html#asm_compliance) | Secrets Manager is now available as a HIPAA\-eligible service\. | June 4, 2018 | 
| [Initial release of service](http://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html) | Documentation is provided for the initial release of AWS Secrets Manager\. | April 4, 2018 | 