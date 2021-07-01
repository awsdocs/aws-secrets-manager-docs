# Granting a rotation function permission to access a separate master secret<a name="permissions-grant-rotation-role-access-to-master-secret"></a>

When you create a Lambda rotation function using the provided AWS Serverless Application Repository template, either by using the console or by using AWS CLI commands, a default policy attached to the function role controls the function actions\. By default, this policy grants access *only* to secrets with this Lambda function configured as the secret rotation function\.

If the credentials in the secret don't allow users permission to change their password on the secured database or service, then you need to use a separate set of credentials with elevated permissions \(a *superuser*\) to change this secret credentials during rotation\. Secrets Manager stores the superuser credentials in a separate "master" secret\. Then, when you rotate your secret, the Lambda rotation function signs in to the database or service with the master credentials to change or update the secret credentials\. If you choose to implement this strategy, then you must add an additional statement to the role policy attached to the function that grants access to this master secret, in addition to the main secret\.

If you use the console to configure a rotation with a strategy using a master secret, then you can select the master secret when you configure rotation for the secret\. 

**Important**  
You must have the **GetSecretValue** permission for the master secret to select it in the console\.

After you configure rotation in the console, you must manually perform the following steps to grant the Lambda function access to the master secret\.

**To grant a Lambda rotation function access to a master secret**  
Follow the steps in one of the following tabs:

------
#### [ Using the console ]

1. When you finish creating a secret with rotation enabled or editing your secret to enable rotation, the console displays a message similar to the following:

   ```
   Your secret MyNewSecret has been successfully stored [and secret rotation is enabled].
   To finish configuring rotation, you need to grant the role MyLambdaFunctionRole permission to retrieve the secret <ARN of master secret>.
   ```

1. Copy the ARN of the master secret in the message to your clipboard\. You use it in a later step\.

1. The role name in the preceding message provides a link to the IAM console, and navigates directly to the role for you\. Choose that link\.

1. On the **Permissions** tab, there can be one or two inline policies\. Secrets Manager names one as `SecretsManager<Name of Template>0`\. The template contains the EC2\-related permissions required when both the rotation function and you run your secured service in a VPC, and not directly accessible from the internet\. The other template named `SecretsManager<Name of Template>1`\.contains the permissions to enable the rotation function to call Secrets Manager operations\. Open the policy \(the one ending with "1"\) by choosing the expand arrow to the left of the policy and examining the permissions\.

1. Choose **Edit policy**, and then perform the steps in one of the following tabs:

------
#### [ Using the IAM Visual Editor ]

   On the **Visual editor** tab, choose **Add additional permissions**, and then set the following values:
   + For **Service**, choose **Secrets Manager**\.
   + For **Actions**, choose **GetSecretValue**\.
   + For **Resources**, choose **Add ARN** next to the **secret** resource type entry\.
   + In the **Add ARN\(s\)** dialog box, paste the ARN of the master secret you copied previously\.

------
#### [ Using the JSON editor ]

   On the **JSON** tab, examine the top of the script\. Between lines 3 \(`"Statement": [`\) and line 4 \( `{` \), enter the following lines:

   ```
           {
               "Action": "secretsmanager:GetSecretValue",
               "Resource": "arn:aws:secretsmanager:region:123456789012:secret:MyDatabaseMasterSecret",
               "Effect": "Allow"
           },
   ```

------

1. After you finish editing the policy, choose **Review policy**, and then **Save changes**\.

1. You can now close the IAM console and return to the Secrets Manager console\.

------

**Note**  
For a complete list of actions, resources, and context keys you can use with Secrets Manager, see [Actions, Resources, and Condition Keys for AWS Secrets Manager](https://docs.aws.amazon.com/IAM/latest/UserGuide/list_awssecretsmanager.html)\.