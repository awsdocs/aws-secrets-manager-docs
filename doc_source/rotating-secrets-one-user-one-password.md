# Rotating AWS Secrets Manager Secrets for One User with a Single Password<a name="rotating-secrets-one-user-one-password"></a>

You can configure AWS Secrets Manager to automatically rotate the secret for a secured resource\. In this topic, we describe configuring rotation for a system that allows you to create a single user with a single password\. You can change the password for the user when needed\. This scenario provides simplicity, but doesn't provide the most highly available solution\. Clients can continue to access the secured resource while the password changes\. This can possibly result in some "access denied" situations\. 

The time lag that can occur between the change of the actual password and the change in the corresponding secret that tells the client which password to use incurs a risk\. This risk can increase if you host the secured resource on a "server farm" where the password change takes time to propagate to all member servers\. However, with an [appropriate retry strategy](http://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/), this risk can be significantly mitigated\.

A common scenario for services owned by someone other than the user accessing the service\. The owner of the service allows the customer create ***one*** user account—often with information such as the user email address as the user name, or at least as a uniqueness key\. The service typically allows the user to change the password as often as required\. But, the service doesn't allow the user to create additional users or to change the user name\.

## How Rotation Uses Labels to Manage a Single User with Changing Passwords<a name="about-labels-rotating-one-user-one-password"></a>

The following explains this scenario in more detail:

1. The scenario starts with an application accessing the secured resource, the database, by using the credentials stored in a secret with a single version "A"\. The A version of the secret has the staging label `AWSCURRENT` attached\. The application retrieves the secret by requesting the version with the staging label `AWSCURRENT`, Steps 1 and 2 in the following graphic\. The application then uses the credentials in that version of the secret to access the database, Step 3, to retrieve the data the application needs, Step 4\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/secret-rotate-1a.png)

1. The secret rotation process creates a new version "B" of the secret, not a new secret but a new version of the same secret\. This version B secret initially has the staging label `AWSPENDING` attached by the rotation process\. Secret version B receives a new generated password\. As soon as Secrets Manager successfully stores the secret, the rotation process changes the password for the user in the database authentication system\. At this point, between changing the password on the database and moving the label to the new version of the secret, client sign\-on failures can occur in Step 4\. Because of this risk, it's vital that the rotation process immediately proceed to the next step\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/secret-rotate-1b-failure.png)

1. Secret version B becomes the live password, and the rotation process moves the `AWSCURRENT` staging label from the A version to the new B version of the secret\. This also automatically moves the `AWSPREVIOUS` staging label to the version previously labeled with the `AWSCURRENT` staging label\. This allows the secret to act as "last known good" in case there's a need for recovery\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/secret-rotate-1c.png)

1. The next request from the custom application now receives the B version of the secret because B now has the `AWSCURRENT` label\. At this point, the customer application depends on the new version of the secret\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/secret-rotate-1d.png)

## Configuring Rotation to Change Passwords Only<a name="configure-rotating-one-user-one-password"></a>

To configure a rotation mechanism for an authentication system that allows you to have only one user, follow the steps in this procedure:

**Configuring rotation for password\-only rotation**

1. Create the single user in the authentication system protecting the secured resource\. Make note of the password\.

1. Create a Secrets Manager secret to store the details of the credentials you created in the previous step:

   1. Sign in to the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

   1. Choose **New secret**\.

   1. For **Select secret type**, choose the option that best fits your service\. Then configure the details for your database or service, including the user name and the initial password\.

   1. For **AWS KMS Encryption Key**, choose the customer master key \(CMK\) to use to encrypt this secret, or leave it set to the **DefaultMasterKey** for the account\. To use the default key, the credentials accessing the secret must be from the same account that owns the secret\. If the user credentials reside in a different account, then you must create and specify the Amazon Resource Namer \(ARN\) of a custom CMK\.

   1. For **Select rotation period**, choose or type the number of days between rotations\.
**Note**  
When you use the Secrets Manager console to configure rotation, by default, Secrets Manager automatically enables expiration and sets to the number of days in a rotation cycle \+ 7\. 

   1. For **What credentials can rotate this secret?**, choose **Use the same credentials**\.
**Note**  
This example scenario assumes the user can change their password, and you can't use a second user with permissions to change the password on the first user\.

   1. Choose **Next step**\.

   1. Type a **Secret name** and optional **Description**\. You can also choose to add tags\.

   1. Choose **Store secret**\.

1. Examine your new secret to get the ARN of the Lambda rotation function\.

   1. On the **Secrets** list page, choose the name of the secret you created in step 2\.

   1. In the **Secret details / Secret rotation** section, choose the ARN of the rotation function to open it in Lambda\.

   1. Customize the rotation function to meet your specific requirements\. You can use the following requirements for each step as the basis for writing the function\. 
      + **createSecret step**:
        + Retrieve the `AWSCURRENT` version of the secret by using the `GetSecretValue` operation\.
        + Extract the protected secret text from the `SecretString` field, and store it in a structure you can modify\.
        + Generate a new password by using an algorithm to generate passwords with the maximum length and complexity requirements supported by the protected resource\.
        + Overwrite the `password` field in the structure with the new one you generated in the previous step\. Keep all other details, such as `username` and the connection details the same\.
        + Store the modified copy of the secret structure by passing it as the `SecretString` parameter in a call to `PutSecretValue`\. Secrets Manager labels the new version of the secret with `AWSPENDING`\.
      + **setSecret step**:
        + Retrieve the `AWSPENDING` version of the secret by using the `GetSecretValue` operation\.
        + Issue commands to the secured resource authentication system to change the existing user password to the one stored in the new `AWSPENDING` version of the secret\.
      + **testSecret step**:
        + Retrieve the `AWSPENDING` version of the secret by using the `GetSecretValue` operation\.
        + Issue commands to the secured resource to attempt to access it by using the credentials in the secret\.
      + **finishSecret step**:
        + Move the label `AWSCURRENT` to the version labeled `AWSPENDING`\. This automatically also moves the staging label `AWSPREVIOUS` to the secret you just removed `AWSCURRENT` from\.
        + \(Optional\) Remove the label `AWSPENDING` from its version of the secret\.

**Important**  
Remember that in this scenario, your users cannot continue operating with the old version while you create and verify the new version\. Therefore, between the time in the `setSecret` phase when you change the password, and the time in the `finishSecret` phase when you move the `AWSCURRENT` label to the new version, your clients may use the wrong credentials and get access denied errors\. Be sure to include [reasonable retry functionality](http://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/) in your client application for this situation to help mitigate this risk\.