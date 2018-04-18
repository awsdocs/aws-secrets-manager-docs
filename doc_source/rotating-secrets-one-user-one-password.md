# Rotating AWS Secrets Manager Secrets for One User with a Single Password Only<a name="rotating-secrets-one-user-one-password"></a>

You can configure AWS Secrets Manager to automatically rotate the secret for a secured resource\. In this topic we describe how to configure rotation for a system that allows you to create a single user with a single password\. You are allowed to change the password for that user when needed\. This scenario is the simplest, but is not the most highly\-available solution\. Clients can continue to access the secured resource while the password change is in progress, possibly resulting in some "access denied" situations\. The risk is because of the time lag that can occur between the change of the actual password and the change in the corresponding secret which tells the client which password to use\. This risk can increase if the secured resource is hosted on a "server farm" for which the password change takes time to propagate to all member servers\. However, with an appropriate retry strategy, this risk can be significantly mitigated\.

This scenario is a common one for services that are owned by someone other than the user accessing the service\. The owner of the service lets the customer create ***one*** user account, often with something like the user's email address as the user name itself, or at least as a uniqueness key\. The service typically allows the user to change the password as often as is required, but does not allow the user to create additional users or to change the user name\.

## How Rotation Uses Labels to Manage a Single User with Changing Passwords<a name="about-labels-rotating-one-user-one-password"></a>

The following explains this scenario in more detail:

1. The scenario begins with an app that's accessing the secured resource by using the credentials stored in a secret that has a single version "A"\. This A version of the secret has the staging label `AWSCURRENT` attached\. The app retrieves the secret by requesting the version with the staging label `AWSCURRENT` \(steps 1 and 2 in the following graphic\)\. The app then uses the credentials in that version of the secret to access the database \(step 3\) to retrieve the data it needs \(step 4\)\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/secret-rotate-1a.png)

1. The rotation process creates a new version "B" of the secret \(not a new secret, a new version of the same secret\)\. This secret version B initially has the staging label `AWSPENDING` attached by the rotation process\. Secret version B gets a new generated password\. As soon as the secret is successfully stored, the rotation process changes the password for the user in the database's authentication system\. It is at this point, between changing the password on the database and moving the label to the new version of the secret, that client sign\-on failures can occur in step 4\. Because of this risk, it is vital that the rotation process immediately proceed to the next step\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/secret-rotate-1b-failure.png)

1. Because the live password is now the one stored in secret B, the rotation process moves the `AWSCURRENT` staging label from the A version to the new B version of the secret\. This also automatically moves `AWSPREVIOUS` staging label to the version that had previously had the `AWSCURRENT` staging label\. This allows it to act as "last known good" in case there is a need for recovery\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/secret-rotate-1c.png)

1. The next request from the custom app now gets the B version of the secret because B now has the `AWSCURRENT` label\. At this point, the customer app is dependent upon the new version of the secret\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/secret-rotate-1d.png)

## Configuring Rotation to Change Passwords Only<a name="configure-rotating-one-user-one-password"></a>

To configure a rotation mechanism for an authentication system that allows you to have only one user, follow the steps in this procedure:

**To configure rotation for password\-only rotation**

1. Create the single user in the authentication system that protects the secured resource\. Make note of the password that you set\.

1. Create an AWS Secrets Manager secret to store the details of the credentials you created in the previous step:

   1. Sign in to the AWS Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

   1. Choose **New secret**\.

   1. For **Select secret type**, choose the option that best fits your service, and then configure the details for your database or service, including the user name and the initial password\.

   1. For **AWS KMS Encryption Key**, select the CMK that you want to use to encrypt this secret, or leave it set to the **DefaultMasterKey** for the account\. To use the default key, the credentials that access the secret must be from the same account that owns the secret\. If the user's credentials are from a different account, then you must create and specify the ARN of a custom CMK\.

   1. For **Select rotation period**, choose or type the number of days between rotations\.
**Note**  
When you use the Secrets Manager console to configure rotation, by default, expiration is automatically enabled and set to the number of days in a rotation cycle \+ 7\. 

   1. For **What credentials can rotation this secret?**, choose **Use the same credentials**\.
**Note**  
This example scenario assumes that the user is allowed to change their own password and that you cannot use a second user with permissions to change the password on the first user\.

   1. Choose **Next step**\.

   1. Type a **Secret name** and optional **Description**\. You can also choose to add tags if you wish\.

   1. Choose **Store secret**\.

1. Examine your new secret to get the ARN of the Lambda rotation function\.

   1. On the **Secrets** list page, choose the name of the secret you created in the step 2\.

   1. In the Secret details / Secret rotation section, choose the ARN of the rotation function to open it in Lambda\.

   1. Customize the rotation function to meet your specific requirements\. You can use the following requirements for each step as the basis for writing the function\. 
      + **createSecret step**:
        + Retrieve the `AWSCURRENT` version of the secret using the `GetSecretValue` operation\.
        + Extract the protected secret text from the `SecureString` field and store it in a structure that you can modify\.
        + Generate a new password using an algorithm that generates passwords with the maximum length and complexity requirements supported by the protected resource\.
        + Overwrite the `password` field in the structure with the new one you generated in the previous step\. Keep all other details, such as `username` and the connection details the same\.
        + Store the modified copy of the secret structure by passing it as the `SecureString` parameter in a call to `PutSecretValue`\. The new version of the secret is labelled `AWSPENDING`\.
      + **setSecret step**:
        + Retrieve the `AWSPENDING` version of the secret using the `GetSecretValue` operation\.
        + Issue commands to the secured resource's authentication system to change the existing user's password to the one stored in the new `AWSPENDING` version of the secret\.
      + **testSecret step**:
        + Retrieve the `AWSPENDING` version of the secret using the `GetSecretValue` operation\.
        + Issue commands to the secured resource to attempt to access it using the credentials found in the secret\.
      + **finishSecret step**:
        + Move the label `AWSCURRENT` to the version labelled `AWSPENDING`\. This automatically also moves the staging label `AWSPREVIOUS` to the secret that you just removed `AWSCURRENT` from\.
        + Remove the label `AWSPENDING` from its version\.

**Important**  
Remember that in this scenario, there is no opportunity for your users to continue operating with the old version while you create and verify the new version\. Therefore, between the time in the `setSecret` phase when you change the password, and the time in the `finishSecret` phase when you move the `AWSCURRENT` label to the new version, it is possible for your clients to use the wrong credentials and get access denied errors\. Be sure to include reasonable retry functionality in your client app for this situation to help mitigate this risk\.