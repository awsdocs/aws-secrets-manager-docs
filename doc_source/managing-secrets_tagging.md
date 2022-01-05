# Tag secrets<a name="managing-secrets_tagging"></a>

Secrets Manager defines a *tag* as a label consisting of a key that you define and an optional value\. You can use tags to make it easy to manage, search, and filter secrets and other resources in your AWS account\. When you tag your secrets, use a standard naming scheme across all of your resources\. Tags are case sensitive\. Never store sensitive information for a secret in a tag\.

To find secrets with a specific tag, see [Find secrets in AWS Secrets Manager](manage_search-secret.md)\.

Create tags for:
+ **Security/access control** – You can grant or deny access to a secret by checking the tags attached to the secret\. See [Example: Control access to secrets using tags](auth-and-access_examples.md#tag-secrets-abac)\.
+ **Automation** – You can use tags to filter resources for automation\. For example, some customers run automated start/stop scripts to turn off development environments during non\-business hours to reduce costs\. You can create and then check for a tag indicating if a specific Amazon EC2 instance should be included in the shutdown\.
+ **Filtering** – You can find secrets by tags in the console, AWS CLI, and SDKs\. AWS also provides the Resource Groups tool to create a custom console that consolidates and organizes your resources based on their tags\. For more information, see [Working with Resource Groups](https://docs.aws.amazon.com/) in the *AWS Management Console Getting Started Guide*\.

For more information, see [AWS Tagging Strategies](https://aws.amazon.com/answers/account-management/aws-tagging-strategies/) on the *AWS Answers* website\.

You can tag your secrets [when you create them](manage_create-basic-secret.md) or [when you edit them](manage_update-secret.md)\.

**To change tags for your secret \(console\)**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. From the list of secrets, choose your secret\.

1. In the secret details page, in the **Tags** section, choose **Edit**\. Tag key names and values are case sensitive, and tag keys must be unique\. 

## AWS CLI<a name="managing-secrets_tagging-cli"></a>

To change tags for your secret, use the [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/tag-resource.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/tag-resource.html) or [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/untag-resource.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/untag-resource.html) operation\.

**Example**  
The following example adds or replaces the tags with those provided by the `--tags` parameter\. Tag key names and values are case sensitive, and tag keys must be unique\. The parameter is expected to be a JSON array of `Key` and `Value` elements:  

```
$ aws secretsmanager tag-resource --secret-id MySecret2 --tags Key=costcenter,Value=12345
```

**Example**  
The following example AWS CLI command removes the tags with the key "environment" from the specified secret:  

```
$ aws secretsmanager untag-resource --secret-id MySecret2 --tag-keys 'environment'
```
The `tag-resource` command doesn't return any output\. 

## AWS SDK<a name="managing-secrets_tagging-sdk"></a>

To change tags for your secret, use [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_TagResource.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_TagResource.html) or [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UntagResource.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UntagResource.html)\. For more information, see [AWS SDKs](asm_access.md#asm-sdks)\.