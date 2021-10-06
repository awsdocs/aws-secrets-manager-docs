# Tag your secrets<a name="managing-secrets_tagging"></a>

Several AWS services enable you to add *tags* to your resources, and Secrets Manager allows you tag your secrets\. Secrets Manager defines a tag as a simple label consisting of a customer\-defined key and an optional value\. You can use the tags to make it easy to manage, search, and filter the resources in your AWS account\. When you tag your secrets, be sure to follow these guidelines:
+ Use a standardized naming scheme across all of your resources\. Remember tags are case sensitive\.
+ Create tag sets enabling you to perform the following:
  + **Security/access control** – You can grant or deny access to a secret by checking the tags attached to the secret\. See [Example: Control access to secrets using tags](auth-and-access_examples.md#tag-secrets-abac)\.
  + **Cost allocation and tracking** – You can group and categorize your AWS bills by tags\. For more information, see [Using Cost Allocation Tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/cost-alloc-tags.html) in the *AWS Billing and Cost Management User Guide*\.
  + **Automation** – You can use tags to filter resources for automation activities\. For example, some customers run automated start/stop scripts to turn off development environments during non\-business hours to reduce costs\. You can create and then check for a tag indicating if a specific Amazon EC2 instance should be included in the shutdown\.
  + **AWS Management Console** – Some AWS service consoles enable you to organize the displayed resources according to the tags, and to sort and filter by tags\. AWS also provides the Resource Groups tool to create a custom console that consolidates and organizes your resources based on their tags\. For more information, see [Working with Resource Groups](https://docs.aws.amazon.com/) in the *AWS Management Console Getting Started Guide*\.

 Use tags creatively to manage your secrets\. Remember you must never store sensitive information for a secret in a tag\.

You can tag your secrets [when you create them](manage_create-basic-secret.md) or [when you edit them](manage_update-secret.md)\.

For more information, see [AWS Tagging Strategies](https://aws.amazon.com/answers/account-management/aws-tagging-strategies/) on the *AWS Answers* website\.

**To change tags for your secret \(console\)**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. From the list of secrets, choose your secret\.

1. In the secret details page, in the **Tags** section, choose **Edit**\. Tag key names and values are case sensitive, and tag keys must be unique\. 

   

**To change tags for your secret \(AWS CLI\)**
+ Use the [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/tag-resource.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/tag-resource.html) or [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/untag-resource.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/untag-resource.html) operation\.  
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

**To change tags for your secret \(AWS SDK\)**
+ Use [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_TagResource.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_TagResource.html) or [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UntagResource.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UntagResource.html)\. 

  For more information, see:
  + [C\+\+](http://sdk.amazonaws.com/cpp/api/LATEST/namespace_aws_1_1_secrets_manager.html)
  + [Java](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/com/amazonaws/services/secretsmanager/package-summary.html)
  + [PHP](https://docs.aws.amazon.com//aws-sdk-php/v3/api/namespace-Aws.SecretsManager.html)
  + [Python](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/secretsmanager.html)
  + [Ruby](https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/SecretsManager.html)
  + [Node\.js](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/SecretsManager.html)

**To find secrets with a specific tag \(AWS CLI\)**
+ Use [list\-secrets](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_ListSecrets.html)\. The following example finds secrets with the `tag` **costcenter** and the `value` **12345**:

  ```
  $ aws secretsmanager list-secrets --filters Key=tag-key,Values=costcenter Key=tag-value,Values=12345
  ```