# Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users<a name="rotating-secrets-two-users"></a>

You can configure AWS Secrets Manager to automatically rotate the secret for a secured resource\. 

In this topic, we describe how to configure rotation for a system that allows you to create and alternate between two users that you can change the password for, when needed\. This enables you to remove the potential for downtime that occurs in the [scenario where you're limited to only one user account](rotating-secrets-one-user-one-password.md)\. In this case, you change more than just the password in a secret version\. Each version must also capture the change in the user, and alternate between each user with each rotation cycle\.

If the administrator enables you to create a third "master" user with the elevated permissions to change the password for the first two users, we recommend that you do so\. This is more secure than allowing the users to have permissions to change their own passwords\. If you do configure the scenario this way, then you need to create a second secret that's used to change the password of the users that are alternated in the first secret\. Create that "master" secret first, so that it's available to reference when you configure the "user" secret\.

The scenario \(described in the following section\), which is implemented by the templates that are provided by Secrets Manager, deactivates the current credentials, but doesn't immediately delete them\. Instead, the version of the secret with those credentials is marked with the staging label `AWSPREVIOUS`\. This preserves those credentials for one additional rotation cycle as the "last known good" credentials\. They are then available for recovery if something happens to the current credentials\. 

The credentials are only truly removed after the second rotation cycle when the user is brought back into active service, as described later in this topic\. This means that if you want a given set of credentials to only be valid for a given number of days, we recommend that you set the rotation period to one\-half of that minus one\. This allows the two rotation cycles to both complete and the credentials to become fully deprecated within the specified time frame\. 

For example, if you have a compliance\-mandated maximum credential lifetime of 90 days, then we recommend that you set your rotation interval to 44 days \(90/2 \- 1 = 44\)\. At day 0, the new credentials are created in a rotation and are stage labeled as `AWSCURRENT`\. On day 44, the next rotation demotes the secret with those credentials to `AWSPREVIOUS`, and clients stop actively using them\. On day 88, the next rotation removes all staging labels from the version\. The version is fully deprecated at this point and the user on the database is recycled with a new password, which starts the cycle over again\.

## How Rotation Uses Labels to Alternate Between Two Users<a name="about-labels-rotating-switch-users"></a>

Using staging labels makes it possible to switch back and forth between two users\. One, labeled `AWSCURRENT`, is actively used by the clients\. When rotation is triggered, the rotation process assigns a new password to the inactive second user\. Those credentials are stored in a new version of the secret\. After verifying that access with the credentials in the new secret version works, the rotation process moves the `AWSCURRENT` label to the new version, which makes it the active version\. The next time the rotation cycle is triggered, the roles of the two user accounts are reversed\.

**Basic Example Scenario**

The following explains this scenario in more detail:

1. The scenario begins with an app that's accessing the database by using a secret that has a single version "A"\. This A version of the secret has the label `AWSCURRENT` attached and contains credentials for the first of the two users\. The app retrieves the secret by requesting the version labeled `AWSCURRENT` \(steps 1 and 2 in the following graphic\)\. The app then uses the credentials in that version of the secret to access the database \(step 3\) to retrieve the data it needs \(step 4\)\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/secret-rotate-1a.png)

1. During the very first rotation, the process creates a new "B" version of the secret \(not a new secret, a new version of the same secret\)\. It basically clones all details of the A version, except that it switches the user name to the second user and generates a new password\. Then the rotation process updates the second user with this new password\. This secret version B initially has the label `AWSPENDING`, which is attached by the rotation process\. Because the custom app is programmed to always request the `AWSCURRENT` label and that hasn't yet moved, the app continues to retrieve and use the original A version of the secret for credentials to access the database\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/secret-rotate-1b.png)

1. After testing the version B secret to ensure it works, the rotation process moves the `AWSCURRENT` label from the A version, and attaches it to the new B version of the secret\. This also automatically moves the `AWSPREVIOUS` staging label to the version that previously had the `AWSCURRENT` staging label\. This allows it to act as "last known good" in case there's a need for recovery\. The `AWSPREVIOUS` version of the secret continues to be available for recovery for one additional rotation cycle\. It will be truly deprecated when it's reused in the cycle that follows\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/secret-rotate-1c.png)

1. The next request from the custom app now gets the B version of the secret because B now has the `AWSCURRENT` label\. The customer app is now using the second user with its new password to access the database\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/secret-rotate-1d.png)

## Configuring Rotation to Switch Between Two Users<a name="configure-rotating-two-users-only"></a>

If your secret is for one of the [supported Amazon RDS databases](intro.md#full-rotation-support), follow the procedures in [Enabling Rotation for an Amazon RDS Database Secret](enable-rotation-rds.md)\.

If instead you want to configure rotation for another service or database, create your Lambda rotation function and customize it using these instructions:

1. Create two users in your database or service\. Make note of the user names and passwords that you use\. Make sure that each user has the ability to change their own password\.

1. Create a secret that contains the credentials of the first user\.

Follow the steps at [Rotating AWS Secrets Manager Secrets for Other Databases or Services](rotating-secrets-create-generic-template.md) to create a generic Lambda rotation function template that you can customize\. When you get to step 7, customize your function by using the following information\.

Use the following logic as the basis for writing your rotation function:
+ **createSecret phase**:
  + Retrieve the `AWSCURRENT` version of the secret by using the `GetSecretValue` operation\.
  + Extract the protected secret text from the `SecretString` field, and place it in a structure that you can manipulate\.
  + Look at the `username` field and determine what the alternate user name is\.
  + Determine whether a user with the alternate user name exists on the database\. If it doesn't, this must be the first time through the rotation process\. Clone the current user and its permissions\.
  + Set the `username` entry in the copy of the secret's structure to the alternate user name\.
  + Generate a new password with the maximum length and complexity requirements that are supported by the protected resource\.
  + Set the `password` entry in the copy of the secret's structure to the new password\.
  + Store the modified copy of the structure into the secret by passing it as the `SecretString` parameter in a call to `PutSecretValue`\. The new version of the secret is labeled `AWSPENDING`\.
+ **setSecret phase**:
  + Retrieve the `AWSPENDING` version of the secret by using the `GetSecretValue` operation\.
  + Extract the user name and password from the `SecretString` field\.
  + Issue commands to the secured resource's authentication system to change the specified user's password to that value\.
+ **testSecret phase**:
  + Retrieve the `AWSPENDING` version of the secret by using the `GetSecretValue` operation\.
  + Issue commands to the secured resource to attempt to access it by using the credentials in the secret\.
+ **finishSecret phase**:
  + Move the label `AWSCURRENT` to the version with the label `AWSPENDING`\.
  + \(Optional\) Remove the label `AWSPENDING` from its version of the secret\.

**Note**  
In this scenario, there's less chance of users getting an error during the rotation of the secret\. They continue to use the old version, while you configure the new version\. Only when the new version is tested and ready do you switch the label to point to the new version and the clients start using it\. However, because some servers are in farms with propagation delays when changing passwords, you should ensure that you include a reasonable delay \(most likely in the `setSecret` step before you test\) to ensure that the password has had time to be propagated to all servers\. Also, be sure to include reasonable retry functionality in your client app for this situation, to help mitigate this risk\.