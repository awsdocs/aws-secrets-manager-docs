# Managing a Resource\-based Policy for a Secret<a name="manage_secret-policy"></a>

In this section, we describe how to attach, retrieve, and remove resource\-based policies from secrets\.

**Topics**
+ [Attaching a Resource\-based Policy to a Secret](#manage_secret-policy_attach)
+ [Retrieving a Resource\-based Policy from a Secret](#manage_secret-policy_retrieve)
+ [Deleting a Resource\-based Policy from a Secret](#manage_secret-policy_delete)

AWS Secrets Manager enables you to attach a permissions policy directly to the secret \(a "resource\-based policy"\) as part of the authorization strategy for a secret\. This is in addition to access from policies that you attach to the users, groups, or roles who need to access the secret\. You can retrieve a resource\-based policy to examine it at a later date, and you can delete the resource\-based policy when you no longer need it\.

## Attaching a Resource\-based Policy to a Secret<a name="manage_secret-policy_attach"></a>

**To attach a permission policy to a secret**  
For details about how to construct a resource\-based policy, see [Overview of Managing Access Permissions to Your Secrets Manager Secrets](auth-and-access_overview.md) and [Using Resource\-based Policies for Secrets Manager](auth-and-access_resource-based-policies.md)\.

Use the following steps to attach a policy to a secret\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-attach-policy-api"></a>

You can use the following command to attach or modify the policy document that grants or denies access to the specified secret\. The policy document must be formatted as JSON structured text\. For more information, see [Using Resource\-based Policies for Secrets Manager](auth-and-access_resource-based-policies.md)\. We recommend that you store your policy document as a text file, and then reference the file in the parameter of the command\.
+ **API/SDK:** [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_PutResourcePolicy.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_PutResourcePolicy.html)
+ **CLI:** [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/put-resource-policy.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/put-resource-policy.html)

**Example**  
The following example CLI command attaches or replaces the resource\-based permission policy that's currently attached to the secret\. The policy document is retrieved from the file `secretpolicy.json`\.  

```
$ aws secretsmanager put-resource-policy --secret-id production/MyAwesomeAppSecret --resource-policy file://secretpolicy.json
{
  "ARN": "arn:aws:secretsmanager:us-west-2:123456789012:secret:production/MyAwesomeAppSecret-a1b2c3",
  "Name": "MyAwesomeAppSecret"
}
```

------

## Retrieving a Resource\-based Policy from a Secret<a name="manage_secret-policy_retrieve"></a>

**To retrieve a permission policy that's attached to a secret**  
Use the following steps to get the text of a resource\-based policy that's attached to a secret\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-retrieve-policy-api"></a>

You can use the following command to retrieve the policy document that's currently attached to the specified secret\.
+ **API/SDK:** [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetResourcePolicy.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_GetResourcePolicy.html)
+ **CLI:** [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-resource-policy.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/get-resource-policy.html)

**Example**  
The following example CLI command gets the text for the resource\-based permission policy that's currently attached to the secret\.  

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

**To delete the permission policy that's attached to a secret**  
Use the following steps to delete the resource\-based policy that's currently attached to the specified secret\.

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-delete-policy-api"></a>

You can use the following command to delete a resource\-based policy that's currently attached to the specified secret\.
+ **API/SDK:** [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DeleteResourcePolicy.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_DeleteResourcePolicy.html)
+ **CLI:** [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/delete-resource-policy.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/delete-resource-policy.html)

**Example**  
The following example CLI command deletes the resource\-based permission policy that's currently attached to the secret\.  

```
$ aws secretsmanager delete-resource-policy --secret-id production/MyAwesomeAppSecret
{
  "ARN": "arn:aws:secretsmanager:us-west-2:123456789012:secret:production/MyAwesomeAppSecret-a1b2c3",
  "Name": "production/MyAwesomeAppSecret"
}
```

------