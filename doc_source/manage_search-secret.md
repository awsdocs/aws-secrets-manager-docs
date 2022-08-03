# Find secrets in AWS Secrets Manager<a name="manage_search-secret"></a>

When you search for secrets without a filter, Secrets Manager matches keywords in the secret name, description, tag key, and tag value\. Searching without filters is not case\-sensitive and ignores special characters, such as space, /, \_, =, \#, and only uses numbers and letters\. When you search without a filter, Secrets Manager analyzes the search string to convert it to separate words\. The words are separated by any change from uppercase to lowercase, from letter to number, or from number/letter to punctuation\. For example, entering the search term `credsDatabase#892` searches for `creds`, `Database`, and `892` in name, description, and tag key and value\.

You can apply the following filters to your search:

Name  
Matches the beginning of secret names; case\-sensitive\. For example, **Name:** **Data** returns a secret named DatabaseSecret, but not databaseSecret or MyData\. 

Description  
Matches the words in secret descriptions, not case\-sensitive\. For example, **Description**: **My Description** matches secrets with the following descriptions:   
+ My Description
+ my description
+ My basic description
+ Description of my secret

Replicated secrets  
You can filter for primary secrets, replica secrets, or secrets that aren't replicated\.

Tag keys  
Matches the beginning of tag keys; case\-sensitive\. For example, **Tag key:** **Prod** returns secrets with the tag Production and Prod1, but not secrets with the tag prod or 1 Prod\.

Tag values  
Matches the beginning of tag values; case\-sensitive\. For example, **Tag value:** **Prod** returns secrets with the tag Production and Prod1, but not secrets with the tag value prod or 1 Prod\. 

Secrets Manager is a regional service and only secrets within the selected region are returned\.

## AWS CLI<a name="manage_search-secret_cli"></a>

To find secrets stored in Secrets Manager, use [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/list-secret.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/list-secret.html)\. These are the filter keys you can use:
+ `description`
+ `name`
+ `tag-key`
+ `tag-value`
+ `primary-region`
+ `all` \(searches all of the above keys\)

The following example searches for secrets with the keyword **conducts** in the description\. 

```
$ aws secretsmanager list-secrets --filters Key=description,Values=conducts
```

## AWS SDK<a name="manage_search-secret_sdk"></a>

To find secrets by using one of the AWS SDKs, use [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_ListSecrets.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_ListSecrets.html)\. For more information, see [AWS SDKs](asm_access.md#asm-sdks)\.

