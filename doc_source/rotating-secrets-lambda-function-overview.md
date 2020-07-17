# Overview of the Lambda Rotation Function<a name="rotating-secrets-lambda-function-overview"></a>

AWS Secrets Manager uses an AWS Lambda function to perform the actual rotation of a secret\. If you use your secret for one of the [supported Amazon RDS databases](intro.md#full-rotation-support), then Secrets Manager provides the Lambda function for you\. And Secrets Manager automatically customizes the function to meet the requirements of the specified database\. If you use your secret for another service, then you must provide the code for the Lambda function\.

When a configured rotation schedule or a manual process triggers rotation , Secrets Manager calls the Lambda function several times, each time with different parameters\. The Lambda function performs several tasks throughout the process of rotating a secret\. The `Step` parameter in the request specifies the task performed for each request\.

Secrets Manager invokes the Lambda function with the following JSON request structure of parameters:

```
{
  "Step" : "request.type",
  "SecretId" : "string",
  "ClientRequestToken" : "string"
}
```

The following describes the parameters of the request: 
+ **Step** – Specifies the part of the rotation function behavior to invoke\. Each of the different values identifies a step of the rotation process\. The following section [The Steps of the Lambda Rotation Function](#rotation-explanation-of-steps) explains each step in detail\. The separation into independently invoked steps enables the AWS Secrets Manager team to add additional functionality to occur between steps\.
+ **secretId** – The ID or Amazon Resource Name \(ARN\) for the secret to rotate\. Secrets Manager assigns an ARN to every secret when you initially create the secret\. The version rotating automatically becomes the **default** version labeled `AWSCURRENT`\.
+ **clientRequestToken** – A string Secrets Manager provides to the Lambda function\. You must pass the string to any Secrets Manager APIs you call from within the Lambda function\. Secrets Manager uses this token to ensure the idempotency of requests during any required retries caused by failures of individual calls\. This value is a [UUID\-type](https://wikipedia.org/wiki/Universally_unique_identifier) value to ensure uniqueness within the specified secret\. This value becomes the `SecretVersionId` of the new version of the secret\.

 The same `secretId` and `clientTokenRequest` invoke every step\. Only the `Step` parameter changes with each call\. This prevents you from storing any state between steps\. The parameters provide all of the necessary information—or as part of the information in the versions accessed with the `AWSPENDING` or `AWSCURRENT` labels\.

For descriptions of the specific tasks to be performed in each step for the different rotation strategies, see the following topics:
+ [Rotating AWS Secrets Manager Secrets for One User with a Single Password](rotating-secrets-one-user-one-password.md)
+ [Rotating AWS Secrets Manager Secrets by Alternating Between Two Existing Users](rotating-secrets-two-users.md)
+ [Rotating AWS Secrets Manager Secrets For One User Supporting Multiple Credentials](rotating-secrets-one-user-multiple-passwords.md)

## The Steps of the Lambda Rotation Function<a name="rotation-explanation-of-steps"></a>

The functionality built into the Lambda rotation function breaks down into distinct steps\. The `Step` parameter invokes each step by calling the function with one of the parameter values\.

In this release of Secrets Manager, Secrets Manager calls the steps automatically in sequence\. As soon as one step ends, Secrets Manager immediately calls the Lambda function to invoke the next step\. 

When you specify a secret for one of the supported Amazon RDS databases, Secrets Manager uses a standard Lambda function to rotate the secret\. Secrets Manager provides the Lambda function , but you can modify the Lambda function to meet your organizational specific rotation requirements\.

### The createSecret Step<a name="phase-makesecret"></a>

In this step, the Lambda function generates a new version of the secret\. Depending on your scenario, this can be as simple as just generating a new password\. Or you can generate values for a completely new set of credentials, including a user name and password appropriate for the secured resource\. Secrets Manager stores these values as a new version of the secret\. Secrets Manager clones the other values in the secret that don't need to change, such as the connection details, from the existing version of the secret\. Secrets Manager then labels the new version of the secret with the staging label `AWSPENDING` to mark it as the **in\-process** version of the secret\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/Rotation%20Step%201.png)

### The setSecret Step<a name="phase-setsecret"></a>

In this step, the rotation function retrieves the version of the secret labeled `AWSPENDING` from Secrets Manager, the version you just created in the previous step\. The rotation function then invokes the database or service identity service to change the existing password, or to create new credentials to match the new ones in the secret\. If you create a new user, then the function must clone the permissions from the previous user\. Then the new user can continue to perform as needed within your custom application\. 

To change a password or to create new credentials in the database or service authentication system, you must allow the Lambda function to perform these tasks\. Considered administrative tasks, the tasks require permissions you typically don't give users\. We recommend you use a *second* set of credentials with permissions to change the password or create new users for the main secret, as dictated by your rotation strategy\. We refer to these credentials as the *master secret*, and Secrets Manager stores them as a separate secret from the main secret\. The rotation function stores the ARN of this master secret in the main secret for use by the rotation function\. The master secret should never be accessed by your end user custom application\. Only the Lambda rotation function of can access the main secret, to update or create new credentials in the database when rotation occurs\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/Rotation%20Step%202.png)

### The testSecret Step<a name="phase-verifysecret"></a>

This step of the Lambda function verifies the `AWSPENDING` version of the secret by using it to access the secured resource in the same way your custom application accesses the secured resource\. If the application needs read\-only access to the database, then the function should verify the test reads succeed\. If the application must write to the database, then the function should perform some test writes to verify that level of access\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/Rotation%20Step%203.png)

### The finishSecret Step<a name="phase-finishsecret"></a>

This step performs any resource\-specific finalization on this version of the secret\. When complete, the last step requires the Lambda function to move the label `AWSCURRENT` from the current version to this new version of the secret so your clients start using it\. You can also remove the `AWSPENDING` label, but technically not required\. At this point, the basic rotation completes\. All of your clients use the new version of the secret\. The old version receives the `AWSPREVIOUS` staging label, and available for recovery as the last known good version of the secret, if needed\. The old version with the `AWSPREVIOUS` staging label no longer has any staging labels attached, so Secrets Manager considers the old version deprecated and subject to deletion\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/images/Rotation%20Step%204.png)