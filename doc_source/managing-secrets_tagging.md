# Tag AWS Secrets Manager secrets<a name="managing-secrets_tagging"></a>

Secrets Manager defines a *tag* as a label consisting of a key that you define and an optional value\. You can use tags to make it easy to manage, search, and filter secrets and other resources in your AWS account\. When you tag your secrets, use a standard naming scheme across all of your resources\. For more information, see the [Tagging Best Practices](https://docs.aws.amazon.com/whitepapers/latest/tagging-best-practices/tagging-best-practices.html) whitepaper\.

You can grant or deny access to a secret by checking the tags attached to the secret\. For more information, see [Example: Control access to secrets using tags](auth-and-access_examples.md#tag-secrets-abac)\.

You can find secrets by tags in the console, AWS CLI, and SDKs\. AWS also provides the [Resource Groups](https://docs.aws.amazon.com/ARG/latest/userguide/resource-groups.html) tool to create a custom console that consolidates and organizes your resources based on their tags\. To find secrets with a specific tag, see [Find secrets in AWS Secrets Manager](manage_search-secret.md)\. Secrets Manager doesn't support tag\-based cost allocation\.

Tags are case sensitive\. Never store sensitive information for a secret in a tag\. You can tag your secrets when you create them or when you edit them\.

**To change tags for your secret \(console\)**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. From the list of secrets, choose your secret\.

1. In the secret details page, in the **Tags** section, choose **Edit**\. Tag key names and values are case sensitive, and tag keys must be unique\. 

## AWS CLI<a name="managing-secrets_tagging-cli"></a>

**Example Add a tag to a secret**  
The following [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/tag-resource.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/tag-resource.html) example shows how to attach a tag with shorthand syntax\.  

```
aws secretsmanager tag-resource \
            --secret-id MyTestSecret \
            --tags Key=FirstTag,Value=FirstValue
```

**Example Add multiple tags to a secret**  
The following [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/tag-resource.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/tag-resource.html) example attaches two key\-value tags to a secret\.  

```
aws secretsmanager tag-resource \
            --secret-id MyTestSecret \
            --tags '[{"Key": "FirstTag", "Value": "FirstValue"}, {"Key": "SecondTag", "Value": "SecondValue"}]'
```

**Example Remove tags from a secret**  
The following [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/untag-resource.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/untag-resource.html) example removes two tags from a secret\. For each tag, both key and value are removed\.  

```
aws secretsmanager untag-resource \
            --secret-id MyTestSecret \
            --tag-keys '[ "FirstTag", "SecondTag"]'
```

## AWS SDK<a name="managing-secrets_tagging-sdk"></a>

To change tags for your secret, use [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_TagResource.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_TagResource.html) or [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UntagResource.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_UntagResource.html)\. For more information, see [AWS SDKs](asm_access.md#asm-sdks)\.