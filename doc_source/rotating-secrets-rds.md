# Rotating Secrets for Supported Amazon RDS Databases<a name="rotating-secrets-rds"></a>

You can configure AWS Secrets Manager to automatically rotate the secret for an Amazon RDS database\. Secrets Manager uses a Lambda function that Secrets Manager provides\.

**Supported Amazon RDS Databases**<a name="rds-supported-database-list"></a>

For the purposes of the Amazon RDS options in Secrets Manager, a "supported" database means that when you enable rotation, Secrets Manager provides a complete, ready\-to\-run Lambda rotation function that's designed for that database\. For any other type of Amazon RDS database, you can still rotate your secret\. However, you must use the workflow for **Other database**\. For those instructions, see [Rotating AWS Secrets Manager Secrets for Other Databases or Services](rotating-secrets-other.md)\. 

The following list shows the Amazon RDS databases for which Secrets Manager provides and configures a complete, ready\-to\-use rotation function for you, without any additional steps\.
+ Amazon Aurora on Amazon RDS
+ MySQL running on Amazon RDS
+ PostgreSQL running on Amazon RDS

When you enable rotation for a secret with **Credentials for RDS database** as the secret type, Secrets Manager automatically creates and configures a Lambda rotation function for you, and then equips your secret with the Amazon Resource Name \(ARN\) of the function\. Secrets Manager creates the IAM role that's associated with the function and configures it with all of the required permissions\. 

If your Amazon RDS database is running in a VPC provided by Amazon VPC, Secrets Manager also configures the Lambda function to communicate with that VPC\. The only requirement of the VPC is that it must have an NAT gateway to enable the Lambda rotation function to query the Secrets Manager service endpoint on the internet\. 

Otherwise, you typically only need to provide a few details that determine which template is used to construct the Lambda function:
+ **Specify the secret that has credentials with permissions to rotate the secret**: Sometimes the user can change their own password\. Other times, the user has very restricted permissions and can't change their own password\. Instead, you must use the credentials for a different administrator or "super" user to change the user's credentials\. 

  You must specify which secret the rotation function can use to rotate the credentials on the secured database:
  + **Use this secret**: Choose this option if the credentials in the current secret have permissions in the database to change its own password\. Choosing this option causes Secrets Manager to implement a Lambda function with a rotation strategy that changes the password for a single user with each rotation\. For more information about this rotation strategy, see [Rotating AWS Secrets Manager Secrets for One User with a Single Password](rotating-secrets-one-user-one-password.md)\.
**Considerations**  
This option is the "lower availability" option\. This is because sign\-in failures can occur between the moment when the old password is removed by the rotation and the moment when the updated password is made accessible as the new version of the secret\. This time window is typically very short—on the order of a second or less\. If you choose this option, make sure that your client apps implement an appropriate ["backoff and retry with jitter" strategy](http://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/) in their code\. The apps should generate an error only if sign\-in fails several times over a longer period of time\.
  + **Use a secret that I have previously stored in AWS Secrets Manager**: Choose this option if the credentials in the current secret have more restrictive permissions and can't be used to update the credentials on the secured service\. Or choose this if you require high availability for the secret\. To choose this option, create a separate "master" secret with credentials that have permission to create and update credentials on the secured service\. Then choose that master secret from the list\. Choosing this option causes Secrets Manager to implement a Lambda function\. This Lambda function has a rotation strategy that clones the initial user that's found in the secret\. Then it alternates between the two users with each rotation, and updates the password for the user that's becoming active\. For more information about this rotation strategy, see [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)\.
**Considerations**  
This is the "high availability" option because the old version of the secret continues to operate and handle service requests while the new version is prepared and tested\. The old version isn't deprecated until after the clients switch to the new version\. There's no downtime while changing between versions\.  
This option requires the Lambda function to clone the permissions of the original user and apply them to the new user\. The function then alternates between the two users with each rotation\.  
If you ever need to change the permissions granted to the users, ensure that you change permissions for both users\.
+ **You can customize the function**: You can tailor the Lambda rotation function that's provided by Secrets Manager to meet your organization's requirements\. For example, you could extend the [**testSecret** phase of the function](rotating-secrets-lambda-function-overview.md#phase-verifysecret) to test the new version with application\-specific checks to ensure that the new secret works as expected\. For instructions, see [Customizing the Lambda Rotation Function Provided by Secrets Manager](rotating-secrets-customize-rds-lambda.md)\.

**Topics**
+ [Enabling Rotation for an Amazon RDS Database Secret](enable-rotation-rds.md)
+ [Customizing the Lambda Rotation Function Provided by Secrets Manager](rotating-secrets-customize-rds-lambda.md)