# Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users<a name="rotating-secrets-two-users"></a>

You can configure AWS Secrets Manager to automatically rotate the secret for a secured resource\. 

This topic describes configuring rotation for a system that allows you to create and alternate between two users you can change the password, when needed\. This enables you to remove the potential for downtime that occurs in the [scenario where you're limited to only one user account](rotating-secrets-one-user-one-password.md)\. In this case, you change more than just the password in a secret version\. Each version must also capture the change in the user, and alternate between each user with each rotation cycle\.

If the administrator enables you to create a third "master" user with the elevated permissions to change the password for the first two users, we recommend you do so\. This provides more security than allowing the users to have permissions to change their own passwords\. If you do configure the scenario this way, then you need to create a second secret used to change the password of the users alternated in the first secret\. Create that "master" secret first, so that you can use it for reference when you configure the "user" secret\.

Secrets Manager provides templates to implement the following scenario which deactivates the current credentials, but doesn't immediately delete them\. Instead, Secrets Manager marks the version of the secret with current credentials with the staging label `AWSPREVIOUS`\. This preserves those credentials for one additional rotation cycle as the "last known good" credentials\. Secrets Manager keeps the credentials available for recovery if something happens to the current credentials\. 

Secrets Manager only removes the credentials after the second rotation cycle when the rotation function brings the user back into active service, as described later in this topic\. This means if you want a given set of credentials to only be valid for a given number of days, we recommend you set the rotation period to one\-half of that minus one\. This allows the two rotation cycles to both complete and the credentials to become fully deprecated within the specified time frame\. 

For example, if you have a compliance\-mandated maximum credential lifetime of 90 days, then we recommend you set your rotation interval to 44 days \(90/2 \- 1 = 44\)\. At day 0, Secrets Manager creates the new credentials in a rotation and labels them as `AWSCURRENT`\. On day 44, the next rotation demotes the secret with those credentials to `AWSPREVIOUS`, and clients stop actively using them\. On day 88, the next rotation removes all staging labels from the version\. Secrets Manager fully deprecates the version is fully deprecated at this point and recycles the user on the database with a new password, which starts the cycle over again\.

## How Rotation Uses Labels to Alternate Between Two Users<a name="about-labels-rotating-switch-users"></a>

Using staging labels allows Secrets Manager to switch back and forth between two users\. One, labeled `AWSCURRENT`, actively used by the clients\. When the Lambda function rotation triggers, the rotation process assigns a new password to the inactive second user\. Secrets Manager stores those credentials in a new version of the secret\. After verifying access with the credentials in the new secret version works, the rotation process moves the `AWSCURRENT` label to the new version, which makes it the active version\. The next time triggers, the roles of Secrets Manager reverses the two user accounts\.

**Basic Example Scenario**

The following explains this scenario in more detail:

1. The scenario begins with an application accessing the database by using a secret with a single version "A"\. This A version of the secret has the label `AWSCURRENT` attached and contains credentials for the first of the two users\. The application retrieves the secret by requesting the version labeled `AWSCURRENT` \(steps 1 and 2 in the following graphic\)\. The application then uses the credentials in that version of the secret to access the database \(step 3\) to retrieve the data it needs \(step 4\)\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/secret-rotate-1a.png)

1. During the very first rotation, the process creates a new "B" version of the secret, **not a new secret**, but a **new version of the same secret**\. The process clones all details of the A version, except that it switches the user name to the second user and generates a new password\. Then the rotation process updates the second user with this new password\. This secret version B initially has the label `AWSPENDING`, which is attached by the rotation process\. Because you programmed the custom app to always request the `AWSCURRENT` label and the label hasn't changed, the application continues to retrieve and use the original A version of the secret for credentials to access the database\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/secret-rotate-1b.png)

1. After testing the version B secret to ensure it works, the rotation process moves the `AWSCURRENT` label from the A version, and attaches the label to the new B version of the secret\. This also automatically moves the `AWSPREVIOUS` staging label to the previous version with the `AWSCURRENT` staging label\. This allows it to act as "last known good" in case you need recovery\. The `AWSPREVIOUS` version of the secret continues to be available for recovery for one additional rotation cycle\. Secrets Manager deprecates the secret in the next cycle\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/secret-rotate-1c.png)

1. The next request from the custom application now receives the B version of the secret because B now has the `AWSCURRENT` label\. The custom application uses the second user with the new password to access the database\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/secret-rotate-1d.png)

## Configuring Rotation Between Two Users<a name="configure-rotating-two-users-only"></a>

If you use your secret for one of the [supported Amazon RDS databases](intro.md#full-rotation-support), follow the procedures in [Enabling Rotation for an Amazon RDS Database Secret](enable-rotation-rds.md)\.

If instead you want to configure rotation for another service or database, create your Lambda rotation function and customize it using these instructions:

1. Create two users in your database or service\. Make note of the user names and passwords you use\. Make sure each user has the ability to change their password\.

1. Create a secret with the credentials of the first user\.

Follow the steps at [Rotating AWS Secrets Manager Secrets for Other Databases or Services](rotating-secrets-create-generic-template.md) to create a generic Lambda rotation function template that you can customize\. When you get to step 7, customize your function by using the following information\.

Use the following logic as the basis for writing your rotation function:
+ **createSecret phase**:
  + Retrieve the `AWSCURRENT` version of the secret by using the `GetSecretValue` operation\.
  + Extract the protected secret text from the `SecretString` field, and place it in a file structure you can use\.
  + Look at the `username` field and determine the alternate user name\.
  + Determine if a user with the alternate user name exists on the database\. If it doesn't, this must be the first time through the rotation process\. Clone the current user and the permissions\.
  + Set the `username` entry in the copy of the secret structure to the alternate user name\.
  + Generate a new password with the maximum length and complexity requirements supported by the protected resource\.
  + Set the `password` entry in the copy of the secret structure to the new password\.
  + Store the modified copy of the structure into the secret by passing it as the `SecretString` parameter in a call to `PutSecretValue`\. Secrets Manager labels the new version of the secret as `AWSPENDING`\.
+ **setSecret phase**:
  + Retrieve the `AWSPENDING` version of the secret by using the `GetSecretValue` operation\.
  + Extract the user name and password from the `SecretString` field\.
  + Issue commands to the secured resource authentication system to change the specified user password to that value\.
+ **testSecret phase**:
  + Retrieve the `AWSPENDING` version of the secret by using the `GetSecretValue` operation\.
  + Attempt to access the secured resource to attempt access by using the credentials in the secret\.
+ **finishSecret phase**:
  + Move the label `AWSCURRENT` to the version with the label `AWSPENDING`\.
  + \(Optional\) Remove the label `AWSPENDING` from its version of the secret\.

**Note**  
In this scenario, there's less chance of users getting an error during the rotation of the secret\. Users continue to use the old version, while you configure the new version\. Only after Secrets Manager tests the new version, you switch the label to point to the new version and the clients start using it\. However, because some servers reside in server farms with propagation delays when changing passwords, you should ensure you include a reasonable delay, most likely in the `setSecret` step before you test, to ensure that the password has time to propagate to all servers\. Also, be sure to include reasonable retry functionality in your client application for this situation, to help mitigate this risk\.