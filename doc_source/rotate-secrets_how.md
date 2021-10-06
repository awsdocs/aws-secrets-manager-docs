# How rotation works<a name="rotate-secrets_how"></a>

To rotate a secret, Secrets Manager calls a Lambda function according to the schedule you set up\. During rotation, Secrets Manager calls the same function several times, each time with different parameters\. The rotation function does the work of rotating the secret\. There are four steps to rotating a secret, which correspond to four steps in the Lambda rotation function:

**Step 1: Create a new version of the secret \(`createSecret`\)**  
The first step of rotation is to create a new version of the secret\. Depending on your [rotation strategy](https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotating-secrets_strategies.html), the new version can contain a new password, a new username and password, or more secret information\. Secrets Manager labels the new version with the staging label `AWSPENDING`\.

**Step 2: Change the credentials in the database or service \(`setSecret`\)**  
Next, rotation changes the credentials in the database or service to match the new credentials in the `AWSPENDING` version of the secret\. Depending on your [rotation strategy](https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotating-secrets_strategies.html), this step can create a new user with the same permissions as the existing user\. 

**Step 3: Test the new secret version \(`testSecret`\)**  
Next, rotation tests the `AWSPENDING` version of the secret by using it to access the database or service\. Rotation functions based on [Rotation function templates](reference_available-rotation-templates.md) test the new secret by using read access\. Depending on the type of access your applications need, you can update the function to include other access such as write access\. See [Customize a Lambda rotation function for Secrets Manager](rotate-secrets_customize.md)\. 

**Step 4: Finish the rotation \(`finishSecret`\)**  
Finally, rotation moves the label `AWSCURRENT` from the previous secret version to this version\. Secrets Manager adds the `AWSPREVIOUS` staging label to the previous version, so that you retain the last known good version of the secret\. 

During rotation, Secrets Manager logs events that indicate the state of rotation\. For more information, see [Secrets Manager non\-API events](monitoring.md#logging-non-API-events)\.

After rotation is successful, applications that [](retrieving-secrets.md) from Secrets Manager automatically get the updated credentials\. For more details about how each step of rotation works, see the [Secrets Manager rotation function templates](reference_available-rotation-templates.md)\.

To turn on automatic rotation, see: 
+ [Automatically rotate an Amazon RDS, Amazon DocumentDB, or Amazon Redshift secret](rotate-secrets_turn-on-for-db.md) 
+ [Automatically rotate another type of secret](rotate-secrets_turn-on-for-other.md)