# Rotation strategies<a name="rotating-secrets_strategies"></a>

There are three main strategies for rotating a secret: 
+ The secret contains credentials for a user that can update itself in the service or database\. Use the single\-user strategy\.

  While a single user credential is rotating, there is potentially a short period of time, typically a few seconds, between the time you change the password and when the clients begin to use the newer password\. Use [Exponential backoff and jitter](https://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/) to mitigate any outage during a rotation\.
+ If your secret contains credentials for a user that is not permitted to update itself in the service or database, use the multi\-user strategy\. The first secret contains the service or database credentials, and the second secret contains credentials of a superuser who can update the first user's password\.
+ If you need high availability for your secret, use the alternate\-user strategy\. In this scenario, you can create one user the rotation process clones to create two users with equal access to the secured resource\. The rotation process alternates between the two users\. Secrets Manager changes the password, and tests the "inactive" one while your users continue to access the database or service by using the credentials in the "active" secret\. 

  While your clients access the secured resource with one user name by querying for the version with the default staging label `AWSCURRENT` attached, the rotation function changes the password on the inactive second user\. The rotation function stores the updated password in a new version of the secret with the staging label `AWSCURRENT`\. After testing, you move the staging label `AWSCURRENT` to the new version pointing to the alternate user and the new password\. All of the clients immediately begin accessing the secured resource with the alternate user and the updated password\. 

  When the time arrives for the next rotation, you change the password on the original user account now idle\. This creates another new version of the secret and repeats the cycle\.

  This scenario requires that the second secret contains credentials for an administrator or superuser with permissions to change the password on both users\.
+ **You can create new credentials for a single user\.** Some systems enable you to create a single user with multiple sets of access credentials\. Each access credential provides a complete set of credentials and operates independently of the other\. You can delete and recreate the first access credential while Secrets Manager uses the second access credential\. Then you can switch all of your clients over to the new first access credential\. The next time you rotate, you delete and recreate the second access credential while customers to continue to use the second\. 

**Topics**
+ [Rotating AWS Secrets Manager secrets for one user with a single password](rotating-secrets-one-user-one-password.md)
+ [Rotating AWS Secrets Manager secrets by alternating between two existing users](rotating-secrets-two-users.md)
+ [Rotating AWS Secrets Manager secrets for one user supporting multiple credentials](rotating-secrets-one-user-multiple-passwords.md)