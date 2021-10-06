# Permissions for users in a different account<a name="auth-and-access_examples_cross"></a>

To allow users in one account to access secrets in another account \(*cross\-account access*\), you must allow access both in a resource policy and in an identity policy\. This is different than granting access to identities in the same account as the secret\.

You must also allow the identity to use the KMS key that the secret is encrypted with\. This is because you can't use the AWS managed key \(`aws/secretsmanager`\) for cross\-account access\. Instead, you must encrypt your secret with a KMS key that you create, and then attach a key policy to it\. There is a charge for creating KMS keys\. To change the encryption key for a secret, see [Modify a secret](manage_update-secret.md)\.

The following example policies assume you have a secret and encryption key in *Account1*, and an identity in *Account2* that you want to allow to access the secret value\.

**Step 1: Attach a resource policy to the secret in *Account1***
+ The following policy allows *ApplicationRole* in *Account2* to access the secret in *Account1*\. To use this policy, see [Attach a permissions policy to a secret](auth-and-access_resource-policies.md)\.

  ```
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Principal": {
          "AWS": "arn:aws:iam::Account2:role/ApplicationRole"
        },
        "Action": "secretsmanager:GetSecretValue",
        "Resource": "*"
      }
    ]
  }
  ```

**Step 2: Add a statement to the key policy for the KMS key in *Account1***
+ The following key policy statement allows *ApplicationRole* in *Account2* to use the KMS key in *Account1* to decrypt the secret in *Account1*\. To use this statement, add it to the key policy for your KMS key\. For more information, see [Changing a key policy](https://docs.aws.amazon.com/kms/latest/developerguide/key-policy-modifying.html)\.

  ```
  {
    "Effect": "Allow",
    "Principal": {
      "AWS": "arn:aws:iam::Account2:role/ApplicationRole"
    },
    "Action": [
      "kms:Decrypt",
      "kms:DescribeKey"
    ],
    "Resource": "*"
  }
  ```

**Step 3: Attach an identity policy to the identity in *Account2***
+ The following policy allows *ApplicationRole* in *Account2* to access the secret in *Account1* and decrypt the secret value by using the encryption key which is also in *Account1*\. To use this policy, see [Attach a permissions policy to an identity](auth-and-access_iam-policies.md)\. You can find the ARN for your secret in the Secrets Manager console on the secret details page under **Secret ARN**\. Alternatively, you can call [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DescribeSecret.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DescribeSecret.html)\.

  ```
  {
    "Version" : "2012-10-17",
    "Statement" : [
      {
        "Effect": "Allow",
        "Action": "secretsmanager:GetSecretValue",
        "Resource": "SecretARN"
      },
      {
        "Effect": "Allow",
        "Action": "kms:Decrypt",
        "Resource": "arn:aws:kms:Region:Account1:key/EncryptionKey"
      }
    ]
  }
  ```