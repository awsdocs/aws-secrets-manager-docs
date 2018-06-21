# Overview of the Lambda Rotation Function<a name="rotating-secrets-lambda-function-overview"></a>

AWS Secrets Manager uses an AWS Lambda function to perform the actual rotation of a secret\. If your secret is for one of the [supported Amazon RDS databases](intro.md#full-rotation-support), then Secrets Manager provides the Lambda function for you\. And it automatically customizes the function to meet the requirements of the database that you specify\. If your secret is for some other service, then you must provide the code for the Lambda function yourself\.

When rotation is triggered, either by a configured rotation schedule or by you triggering it manually, Secrets Manager calls the Lambda function several times, each time with different parameters\. The Lambda function is expected to perform several tasks throughout the process of rotating a secret\. The task to be performed for each request is specified by the `type` parameter in the request\.

Secrets Manager invokes the Lambda function with the following JSON request structure of parameters:

```
{
  "Step" : "request.type",
  "SecretId" : "string",
  "ClientRequestToken" : "string"
}
```

The parameters of the request are described as follows: 
+ **Step** – Specifies which part of the rotation function's behavior to invoke\. Each of the different values identifies a step of the rotation process\. The following section [The Steps of the Lambda Rotation Function](#rotation-explanation-of-steps) explains each step in detail\. The separation into independently invoked steps enables the AWS Secrets Manager team to add additional functionality in the future that might need to occur between steps\.
+ **secretId** – The ID or Amazon Resource Name \(ARN\) for the secret that you want to rotate\. Every secret is assigned an ARN when you initially create it\. The version that's rotated is automatically the "default" version that's labeled `AWSCURRENT`\.
+ **clientRequestToken** – A string that Secrets Manager provides to the Lambda function\. You must pass it in turn to any Secrets Manager APIs that you call from within the Lambda function\. Secrets Manager uses this token to ensure the idempotency of requests during any required retries \(caused by failures of individual calls\)\. This value is a [UUID\-type](https://wikipedia.org/wiki/Universally_unique_identifier) value to ensure uniqueness within the specified secret\. This value becomes the `SecretVersionId` of the new version of the secret\.

Every step is invoked with the same `secretId` and `clientTokenRequest`\. Only the `Step` parameter changes with each call\. This helps prevent you from having to store any state between steps\. Everything you should need to know is available either in those parameters—or as part of the information in the versions that are accessed with the `AWSPENDING` or `AWSCURRENT` labels\.

For descriptions of the specific tasks that should be performed in each step for the different rotation strategies, see the following topics:
+ [Rotating AWS Secrets Manager Secrets for One User with a Single Password](rotating-secrets-one-user-one-password.md)
+ [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)
+ [Rotating AWS Secrets Manager Secrets For One User that Supports Multiple Credentials](rotating-secrets-one-user-multiple-passwords.md)

## The Steps of the Lambda Rotation Function<a name="rotation-explanation-of-steps"></a>

The functionality that's built into the Lambda rotation function is broken into distinct steps\. Each step is invoked by calling the function with one of the `Step` parameter values\.

In this release of Secrets Manager, the steps are called automatically in sequence\. As soon as one step ends, Secrets Manager immediately calls the Lambda function to invoke the next step\. 

When you specify that a secret is for one of the supported Amazon RDS databases, Secrets Manager still uses a standard Lambda function to rotate the secret\. But Secrets Manager provides the Lambda function\. You don't have to write it\. You can, however, modify it to meet your organization's specific rotation requirements\.

### The createSecret Step<a name="phase-makesecret"></a>

In this step, the Lambda function generates a new version of the secret\. Depending on your scenario, this can be as simple as just generating a new password\. Or you can generate values for a completely new set of credentials, including a user name and password that are appropriate for the secured resource\. These values are then stored as a new version of the secret in Secrets Manager\. Other values in the secret that don't need to change, such as the connection details, are cloned from the existing version of the secret\. The new version of the secret is then given the staging label `AWSPENDING` to mark it as the "in\-process" version of the secret\.

### The setSecret Step<a name="phase-setsecret"></a>

In this step, the rotation function retrieves the version of the secret labeled `AWSPENDING` from Secrets Manager \(the version you just created in the previous step\)\. It then invokes the database's or service's identity service to change the existing password, or to create new credentials that match the new ones in the secret\. If a new user is created, then the function must clone the permissions from the previous user\. This is so that the new user can continue to perform as needed within your custom app\. 

To change a password or to create new credentials in the database's or service's authentication system, you must give the Lambda function permission to carry out such tasks\. These are considered "administrative" tasks that require permissions that you typically don't want your users do to have\. So we recommend that you use a *second* set of credentials that have permissions to change the password or create new users for the 'main' secret, as dictated by your rotation strategy\. We refer to these credentials as the *master secret*, and they're stored as a separate secret from the main secret\. The ARN of this master secret is stored in the main secret for use by the rotation function\. The master secret never needs to be accessed by your end user custom application\. It's instead accessed only by the Lambda rotation function of the main secret, to update or create new credentials in the database when rotation occurs\.

### The testSecret Step<a name="phase-verifysecret"></a>

This step of the Lambda function verifies that the `AWSPENDING` version of the secret is good by trying to use it to access the secured resource in the same way that your custom application would\. If the application needs read\-only access to the database, then the function should verify that the test reads succeed\. If the app needs to be able to write to the database, then the function should perform some test writes of some sort to verify that level of access\.

### The finishSecret Step<a name="phase-finishsecret"></a>

This step performs any resource\-specific finalization on this version of the secret\. When it's done, the last step is for the Lambda function to move the label `AWSCURRENT` from it's current version to this new version of the secret so that your clients start using it\. You can also remove the `AWSPENDING` label, but it's not technically required\. At this point, the basic rotation is done\. The new version of the secret is the one used by all of your clients\. The old version gets the `AWSPREVIOUS` staging label, and is available for recovery as the "last known good" version of the secret, if needed\. The old version that had the `AWSPREVIOUS` staging label no longer has any staging labels attached, so it's considered deprecated and subject to deletion by Secrets Manager\.

### <a name="phase-expiresecret"></a>