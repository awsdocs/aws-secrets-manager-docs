# Managing a Resource\-based Policy for a Secret<a name="manage_secret-policy"></a>

This section describes how to attach, retrieve, and remove resource\-based policies from secrets\.

An AWS account owns all of AWS resources, including the secrets you store in AWS Secrets Manager\. AWS contains resource\-based policies to create or access the resources\. An account administrator can control access to AWS resources by attaching resource\-based policies directly to the resources, the secrets, or to the IAM identities, users, groups, and roles, that need to access the resources\. 

For more information on AWS Identity and Access Management, see the [IAM Policies and Permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html) section of IAM User Guide\.

You can retrieve a resource\-based policy to review it at a later date, and you can delete the resource\-based policy when you no longer need it\.

Use the Secrets Manager console or the `GetResourcePolicy`, `PutResourcePolicy`, and `DeleteResourcePolicy` APIs to retrieve, attach, or delete a resource\-based policy for a secret\. When you attach a resource\-based policy in JSON format in the console, Secrets Manager uses [Zelkova](https://aws.amazon.com/blogs/security/protect-sensitive-data-in-the-cloud-with-automated-reasoning-zelkova/), an automated reasoning engine, and the API, `ValidateResourcePolicy`, to validate that the policy does not grant a wide range of IAM principals access to your secrets\. Alternatively, you can call the `PutResourcePolicy` API with the `BlockPublicPolicy` parameter from the CLI or SDK\. 

For consideration as an acceptable resource\-based policy, the resource\-based policy must only grant access to one or more of the following fixed values: 
+ `aws:SourceArn`
+ `aws:SourceVpc`
+ `aws:SourceVpce`
+ `aws:SourceAccount`

For more information, see [Amazon S3 documentation](https://docs.aws.amazon.com/AmazonS3/latest/dev/access-control-block-public-access.html#access-control-block-public-access-policy-status)\.

AWS uses Zelkova in the following products and Secrets Manager to provide a similar experience:
+ AWS Config
+ Amazon S3
+ AWS Trusted Advisor
+ Amazon Macie
+ Amazon GuardDuty

If your policy does not create a broadly accessible secret, the Secrets Manager Console uses the `PutResourcePolicy` API to attach the policy\. If you do create a resource\-based policy that allows a wide range of IAM principals to access to the secret, the Secrets Manager Console displays an error message and prevents you from attaching the policy\. 

You can still add, modify, retrieve, and delete resource\-based policies using the CLI and API commands\.

## Attaching a Resource\-based Policy to a Secret<a name="manage_secret-policy_attach"></a>

**Attaching a resource\-based policy to a secret**  
For details about constructing a resource\-based policy, see [Overview of Managing Access Permissions to Your Secrets Manager Secrets](auth-and-access_overview.md) and [Using Resource\-based Policies for Secrets Manager](auth-and-access_resource-based-policies.md)\.

Use the following steps to attach a resource\-based policy to a secret\.

------
#### [ Using the Secrets Manager Console ]<a name="proc-attach-policy-console"></a>

You can attach or modify resource\-based policies to secrets using the Secrets Manager Console\. The policy must be formatted as JSON structured text\.

To add a policy to an existing secret, use the following steps:

1. Choose the secret you want to add or modify a resource\-based policy\.

1. Scroll down to the **Resource Permissions \(optional\)** section and choose **Edit permissions**\. Enter your policy, in JSON format, into the code field\.

1. Choose **Save**\.

Secrets Manager validates the policy as part of the **Save** process\. 

**Example**  
The following JSON code example displays an acceptable resource\-based policy:   

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Principal":{
            "AWS":"arn:aws:iam::123456789012:user/admin"
         },
         "Action":"secretsmanager:GetSecretValue",
         "Resource":"arn:aws:secretsmanager:us-east-1:123456789012:secret:consolesecret-m4qxfJ"
      }
   ]
}
```

If Secrets Manager detects an invalid resource\-based policy, Secrets Manager returns an error message depending on the type of error encountered\.
+ Lockout JSON 

  ```
  Your requested changes will not allow you to manage this secret in the future.
  ```  
**Example**  

  ```
  {
    "Version" : "2012-10-17",
    "Statement" : [ {
      "Effect" : "Deny",
      "Principal" : "*",
      "Action" : "secretsmanager:*",
      "Resource" : "*"
    } ]
  }
  ```
+ Broad Access JSON 

  ```
  This resource policy grants broad access to your secret. Secrets Manager does not allow creating and applying such policies in the console.
  ```  
**Example**  

  ```
  {
    "Version" : "2012-10-17",
    "Statement" : [ {
      "Effect" : "Allow",
      "Principal" : "*",
      "Action" : "secretsmanager:*",
      "Resource" : "*"
    } ]
  }
  ```
+ Syntax Error JSON 

  ```
  This resource policy contains a syntax error.
  ```  
**Example**  

  ```
  {
    "Version" : "2012-10-17",
    "Statement" : [ {
      "Effect" : "AllowSomething",
      "Principal" : "*",
      "Action" : "secretsmanager:*",
      "Resource" : "*"
    } ]
  }
  ```
+ Invalid Principal Policy JSON 

  ```
  This resource policy contains an unsupported principal.
  ```  
**Example**  

  ```
  {
    "Version" : "2012-10-17",
    "Statement" : [ {
      "Effect" : "Allow",
      "Principal" : {
        "AWS" : "arn:aws:iam::123456789012:user/invaliduser"
      },
      "Action" : "secretsmanager:GetSecretValue",
      "Resource" : "*"
    } ]
  }
  ```
+ Invalid Service Principal JSON 

  ```
  This resource policy contains an invalid service principal.
  ```  
**Example**  

  ```
  {
    "Version" : "2012-10-17",
    "Statement" : [ {
      "Effect" : "Allow",
      "Principal" : {
        "AWS" : "arn:aws:iam::123456789012:user/iam-user",
        "Service": [
            "invalidservice.amazonaws.com"
          ]
      },
      "Action" : "secretsmanager:GetSecretValue",
      "Resource" : "*"
    } ]
  }
  ```

Secrets Manager displays multiple errors in a single output\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-attach-policy-api"></a>

You can use the following command to attach or modify the policy document to grant or deny access to the specified secret\. The policy document must be formatted as JSON structured text\. For more information, see [Using Resource\-based Policies for Secrets Manager](auth-and-access_resource-based-policies.md)\. We recommend you store your policy document as a text file, and then reference the file in the parameter of the command\.
+ **API/SDK:** [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_PutResourcePolicy.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_PutResourcePolicy.html)
+ **CLI:** [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/put-resource-policy.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/put-resource-policy.html)

**Example**  
The following example CLI command attaches or replaces the resource\-based policy currently attached to the secret\. Secrets Manager retrieves policy document from the file `secretpolicy.json`\.  

```
$ aws secretsmanager put-resource-policy --secret-id production/MyAwesomeAppSecret --resource-policy file://secretpolicy.json 
{
  "ARN": "arn:aws:secretsmanager:us-west-2:123456789012:secret:production/MyAwesomeAppSecret-a1b2c3",
  "Name": "MyAwesomeAppSecret"
  }
```
 Using the optional parameter, `--block-public-policy`, returns an error as Secrets Manager does not allow this type of policy\. Secrets Manager returns the following respone: “An error occurred \(BlockPublicPolicyException\) when calling the PutResourcePolicy operation: You can't attach a resource\-based policy that allows broad access to the secret\.” 

------

## Retrieving a Resource\-based Policy from a Secret<a name="manage_secret-policy_retrieve"></a>

**Retrieving a permission policy attached to a secret**  
Use the following steps to retrieve the text of a resource\-based policy attached to a secret\.

------
#### [ Using the Secrets Manager Console ]<a name="proc-retrieve-policy-console"></a>

To retrieve the resource\-based policy for a secret, use the following steps:

1. Choose the secret from your list of available secrets\.

1. Scroll down to the **Resource Permissions \(optional\)**\.

1. To change the text, choose **Edit Permissions**\.

1. Choose **Save** to save any changes to the resource\-based policy\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-retrieve-policy-api"></a>

You can use the following command to retrieve the policy document currently attached to the specified secret\.
+ **API/SDK:** [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetResourcePolicy.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetResourcePolicy.html)
+ **CLI:** [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-resource-policy.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-resource-policy.html)

**Example**  
The following example CLI command retrieves the text for the resource\-based permission policy currently attached to the secret\.  

```
$ aws secretsmanager get-resource-policy --secret-id production/MyAwesomeAppSecret
{
  "ARN": "arn:aws:secretsmanager:us-west-2:123456789012:secret:production/MyAwesomeAppSecret-a1b2c3",
  "Name": "MyAwesomeAppSecret",
  "ResourcePolicy": "{\"Version\":\"2012-10-17\",\"Statement\":{\"Effect\":\"Allow\",\"Principal\":{\"AWS\":\"arn:aws:iam::111122223333:root\",\"arn:aws:iam::444455556666:root\"},\"Action\":[\"secretsmanager:GetSecret\",\"secretsmanager:GetSecretValue\"],\"Resource\":\"*\"}}"
}
```

------

## Deleting a Resource\-based Policy from a Secret<a name="manage_secret-policy_delete"></a>

**Deleting the resource\-based policy attached to a secret**  
Use the following steps to delete the resource\-based policy currently attached to the specified secret\.

------
#### [ Using the Secrets Manager Console ]<a name="proc-delete-policy-console"></a>

1. Choose the secret from your list of configured secrets\.

1. Scroll down to the **Resource Permissions \(optional\)**\.

1. Choose **Edit permissions**\.

1. Delete the policy text and choose **Save**\.

   Secrets Manager returns a message indicating successful deletion of your resource policy from the secret\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-delete-policy-api"></a>

You can use the following command to delete a resource\-based policy currently attached to the specified secret\.
+ **API/SDK:** [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DeleteResourcePolicy.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DeleteResourcePolicy.html)
+ **CLI:** [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/delete-resource-policy.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/delete-resource-policy.html)

**Example**  
The following example CLI command deletes the resource\-based permission policy currently attached to the secret\.  

```
$ aws secretsmanager delete-resource-policy --secret-id production/MyAwesomeAppSecret
{
  "ARN": "arn:aws:secretsmanager:us-west-2:123456789012:secret:production/MyAwesomeAppSecret-a1b2c3",
  "Name": "production/MyAwesomeAppSecret"
}
```

------