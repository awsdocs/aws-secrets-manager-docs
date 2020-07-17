# Understanding and Customizing Your Lambda Rotation Function<a name="rotating-secrets-lambda-function-customizing"></a>

For details about Lambda rotation functions , see [Overview of the Lambda Rotation Function](rotating-secrets-lambda-function-overview.md)\.

If you choose one of the [supported databases](intro.md#full-rotation-support) for your secret type, AWS Secrets Manager creates and configures the Lambda rotation function for you\. You can enable rotation for those databases by following the steps in [Enabling Rotation for an Amazon RDS Database Secret](enable-rotation-rds.md)\. However, if you want to create a custom Lambda rotation function for another service, then you must follow the steps in [Enabling Rotation for a Secret for Another Database or Service](enable-rotation-other.md)\.

This section describes in detail how the Lambda function operates and how you configure the function to successfully rotate your secrets\.

**Important**  
The Lambda function and the Secrets Manager secret that invokes the function must be in the same AWS Region\. If located different regions, you receive a "Lambda does not exist" error message when you try to add the Amazon Resource Name \(ARN\) of the function to the secret metadata\.

Consider the following scenarios to consider when creating your own Lambda function for rotation\. You apply a scenario based on the features supported by the authentication system protecting the secured resource, and by your security concerns\.
+ **You can change only the password for a single user\.** A common scenario for services owned by someone other than the user accessing the service\. The owner of the service allows the customer to create ***one*** user account, often with a user email address as the user name , or at least as a uniqueness key\. In this scenario, the service typically allows the user to change the password as often as required\. But, the scenario doesn't allow the user to create additional users, and often doesn't allow changing the user name\. 

  Users typically have the ability to change their password, and don't require a separate user with administrator permissions to perform the password change\. However, because you change the password for the single, active user, clients with access to the service with this user might temporarily fail to sign in while processing the password change \. 

  The potential for downtime exists between the time you change the password and when the clients all receive notified to use the newer version of the password\. This should typically be a few seconds, and you must allow for the time in your application code using the secret\. Be sure to [enable retries with some delay in between](http://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/) to be tolerant of this short\-term outage during a rotation\.
+ **You can create two users you can alternate between\.** In this scenario, you can create one user the rotation process clones to create two users with equal access to the secured resource\. The rotation process alternates between the two users\. Secrets Manager changes the password, and tests the "inactive" one while your users continue to access the database or service by using the credentials in the "active" secret\. 

  While your clients access the secured resource with one user name by querying for the version with the default staging label `AWSCURRENT` attached, the rotation function changes the password on the inactive second user\. The rotation function stores the updated password in a new version of the secret with the staging label `AWSCURRENT`\. After testing, you move the staging label `AWSCURRENT` to the new version pointing to the alternate user and the new password\. All of the clients immediately begin accessing the secured resource with the alternate user and the updated password\. 

  When the time arrives for the next rotation, you change the password on the original user account now idle\. This creates another new version of the secret and repeats the cycle\.

  This scenario requires a second secret pointing to an administrator or super user with permissions to change the password on both users\.
+ **You can create new credentials for a single user\.** Some systems enable you to create a single user with multiple sets of access credentials\. Each access credential provides a complete set of credentials and operates independently of the other\. You can delete and recreate the first access credential while Secrets Manager uses the second access credential\. Then you can switch all of your clients over to the new first access credential\. The next time you rotate, you delete and recreate the second access credential while customers to continue to use the second\. 

For additional details and instructions on how to configure each scenario, see the following topics:
+ [Overview of the Lambda Rotation Function](rotating-secrets-lambda-function-overview.md)
+ [Rotating AWS Secrets Manager Secrets for One User with a Single Password](rotating-secrets-one-user-one-password.md)
+ [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)
+ [Rotating AWS Secrets Manager Secrets For One User Supporting Multiple Credentials](rotating-secrets-one-user-multiple-passwords.md)