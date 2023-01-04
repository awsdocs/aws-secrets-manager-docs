# Move hardcoded secrets to AWS Secrets Manager<a name="hardcoded"></a>

If you have plaintext secrets in your code, we recommend that you rotate them and store them in Secrets Manager\. Moving the secret to Secrets Manager solves the problem of the secret being visible to anyone who sees the code, because going forward, your code retrieves the secret directly from Secrets Manager\. Rotating the secret revokes the current hardcoded secret so that it is no longer valid\. 

For database credential secrets, see [Move hardcoded database credentials to AWS Secrets Manager](hardcoded-db-creds.md)\.

Before you begin, you need to determine who needs access to the secret\. We recommend using two IAM roles to manage permission to your secret:
+ A role that manages the secrets in your organization\. For more information, see [Secrets Manager administrator permissions](auth-and-access.md#auth-and-access_admin)\. You'll create and rotate the secret using this role\.
+ A role that can use the secret at runtime, for example in this tutorial you use *RoleToRetrieveSecretAtRuntime*\. Your code assumes this role to retrieve the secret\. In this tutorial, you grant the role only the permission to retrieve one secret value, and you grant permission by using the secret's resource policy\. For other alternatives, see [Next steps](#hardcoded_step-next)\.

**Topics**
+ [Step 1: Create the secret](#hardcoded_step-1)
+ [Step 2: Update your code](#hardcoded_step-2)
+ [Step 3: Update the secret](#hardcoded_step-3)
+ [Next steps](#hardcoded_step-next)

## Step 1: Create the secret<a name="hardcoded_step-1"></a>

The first step is to copy the existing hardcoded secret into Secrets Manager\. If the secret is related to an AWS resource, store it in the same Region as the resource\. Otherwise, store it in the Region that has lowest latency for your use case\.

**To create a secret \(console\)**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose **Store a new secret**\.

1. On the **Choose secret type** page, do the following:

   1. For **Secret type**, choose **Other type of secret**\.

   1. Enter your secret as **Key/value pairs** or in **Plaintext**\. Some examples:  
****    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/secretsmanager/latest/userguide/hardcoded.html)

   1. For **Encryption key**, choose **aws/secretsmanager** to use the AWS managed key for Secrets Manager\. There is no cost for using this key\. You can also use your own customer managed key, for example to [access the secret from another AWS account](auth-and-access_examples_cross.md)\. For information about the costs of using a customer managed key, see [Pricing](intro.md#asm_pricing)\.

   1. Choose **Next**\.

1. On the **Choose secret type** page, do the following:

   1. Enter a descriptive **Secret name** and **Description**\. 

   1. In **Resource permissions**, choose **Edit permissions**\. Paste the following policy, which allows *RoleToRetrieveSecretAtRuntime* to retrieve the secret, and then choose **Save**\.

      ```
      {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Principal": {
              "AWS": "arn:aws:iam::AccountId:role/RoleToRetrieveSecretAtRuntime"
            },
            "Action": "secretsmanager:GetSecretValue",
            "Resource": "*"
          }
        ]
      }
      ```

   1. At the bottom of the page, choose **Next**\.

1. On the **Configure rotation** page, keep rotation off\. Choose **Next**\.

1. On the **Review** page, review your secret details, and then choose **Store**\.

## Step 2: Update your code<a name="hardcoded_step-2"></a>

Your code must assume the IAM role *RoleToRetrieveSecretAtRuntime* to be able to retrieve the secret\. For more information, see [Switching to an IAM role \(AWS API\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-api.html)\.

Next, you update your code to retrieve the secret from Secrets Manager using the sample code provided by Secrets Manager\. 

**To find the sample code**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. On the **Secrets** page, choose your secret\.

1. Scroll down to **Sample code**\. Choose your programming language, and then copy the code snippet\.

In your application, remove the hardcoded secret and paste the code snippet\. Depending on your code language, you might need to add a call to the function or method in the snippet\.

Test that your application works as expected with the secret in place of the hardcoded secret\.

## Step 3: Update the secret<a name="hardcoded_step-3"></a>

The last step is to revoke and update the hardcoded secret\. Refer to the source of the secret to find instructions to revoke and update the secret\. For example, you might need to deactivate the current secret and generate a new secret\.

**To update the secret with the new value**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose **Secrets**, and then choose the secret\.

1. On the **Secret details** page, scroll down and choose **Retrieve secret value**, and then choose **Edit**\.

1. Update the secret and then choose **Save**\. 

Next, test that your application works as expected with the new secret\.

## Next steps<a name="hardcoded_step-next"></a>

After you remove a hardcoded secret from your code, some ideas to consider next:
+ To find hardcoded secrets in your Java and Python applications, we recommend [Amazon CodeGuru Reviewer](https://docs.aws.amazon.com/codeguru/latest/reviewer-ug/welcome.html)\.
+ You can improve performance and reduce costs by caching secrets\. For more information, see [Retrieve secrets from AWS Secrets Manager](retrieving-secrets.md)\.
+ For secrets that you access from multiple Regions, consider replicating your secret to improve latency\. For more information, see [Replicate an AWS Secrets Manager secret to other AWS Regions](create-manage-multi-region-secrets.md)\.
+ In this tutorial, you granted *RoleToRetrieveSecretAtRuntime* only the permission to retrieve the secret value\. To grant the role more permissions, for example to get metadata about the secret or to view a list of secrets, see [Permissions policy examples for AWS Secrets Manager](auth-and-access_examples.md)\. 
+ In this tutorial, you granted permission to *RoleToRetrieveSecretAtRuntime* by using the secret's resource policy\. For other ways to grant permission, see [Attach a permissions policy to an identity](auth-and-access_iam-policies.md)\.