# Understanding and Customizing Your Lambda Rotation Function<a name="rotating-secrets-lambda-function-customizing"></a>

For details about how a Lambda rotation function works, see [Overview of the Lambda Rotation Function](rotating-secrets-lambda-function-overview.md)\.

If you choose one of the [supported databases](intro.md#full-rotation-support) for your secret type, AWS Secrets Manager creates and configures the Lambda rotation function for you\. You can enable rotation for those databases by following the steps in [Enabling Rotation for an Amazon RDS Database Secret](enable-rotation-rds.md)\. However, if you want to create a custom Lambda rotation function for another service, then you must follow the steps in [Enabling Rotation for a Secret for Another Database or Service](enable-rotation-other.md)\.

This section describes in detail how the Lambda function operates and how it needs to be configured to successfully rotate your secrets\.

**Important**  
The Lambda function and the Secrets Manager secret that invokes it must be in the same AWS Region\. If they're in different regions, you get a "Lambda does not exist" error message when you try to add the Amazon Resource Name \(ARN\) of the function to the secret's metadata\.

The following are the primary scenarios to consider when you're creating your own Lambda function for rotation\. The scenario that applies is determined by the features supported by the authentication system that protects the secured resource, and by your security concerns\.
+ **You can change only the password for a single user\.** This is a common scenario for services that are owned by someone other than the user who accesses the service\. The owner of the service lets the customer create ***one*** user account, often with something like the user's email address as the user name itself, or at least as a uniqueness key\. In this scenario, the service typically allows the user to change the password as often as is required\. But, it doesn't allow the user to create additional users, and often doesn't even allow the user name to change\. 

  Users typically have the ability to change their own password, and don't require a separate user with administrator permissions to do the password change\. However, because you're changing the password for the single, active user, clients that access the service with this user might temporarily fail to sign in while the password change is in progress\. 

  The potential for downtime exists between when the password is changed and when the clients all get notified to use the newer version of the password\. This should typically be only on the order of a few seconds, and you must allow for it in your application code that uses the secret\. Be sure to [enable retries with some delay in between](http://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/) to be tolerant of this short\-term outage during a rotation\.
+ **You can create two users that you can alternate between\.** In this scenario, you can create one user that the rotation process clones to make two users that have equal access to the secured resource\. The rotation process alternates between the two users\. It changes the password, and tests the "inactive" one while your users continue to access the database or service by using the credentials in the "active" secret\. 

  While your clients are all accessing the secured resource with one user name \(by querying for the version that has the default staging label `AWSCURRENT` attached\), the rotation function changes the password on the currently inactive second user\. The rotation function stores that updated password in a new version of the secret that has the staging label `AWSCURRENT`\. After testing, you move the staging label `AWSCURRENT` to the new version that points to the alternate user and its new password\. All of the clients immediately begin accessing the secured resource with the alternate user and its updated password\. 

  When it's time for the next rotation, you change the password on the original user account that's now idle\. This creates another new version of the secret and repeats the cycle\.

  This scenario requires a second secret that points to an administrator or super user that has permissions to change the password on both users\.
+ **You can create new credentials for a single user\.** Some systems enable you to create a single user with multiple sets of access credentials\. Each access credential is a complete set of credentials and operates independently of the other\. You can delete and recreate the first access credential while the second access credential is in use\. Then you can switch all of your clients to use the new first access credential\. The next time you rotate, you delete and recreate the second access credential while customers to continue to use the second\. 

For additional details and instructions on how to configure each scenario, see the following topics:
+ [Overview of the Lambda Rotation Function](rotating-secrets-lambda-function-overview.md)
+ [Rotating AWS Secrets Manager Secrets for One User with a Single Password](rotating-secrets-one-user-one-password.md)
+ [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)
+ [Rotating AWS Secrets Manager Secrets For One User that Supports Multiple Credentials](rotating-secrets-one-user-multiple-passwords.md)