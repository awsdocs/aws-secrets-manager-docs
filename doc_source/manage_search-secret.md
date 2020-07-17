# Enhanced Search Capabilities for Secrets in Secrets Manager<a name="manage_search-secret"></a>

Secrets Manager provides an easier method to manage and search secrets based on attributes such as secret names, description, tag keys, and tag values by introducing an enhanced search functionality supported through the AWS console, AWS API, and AWS CLI\. 

If you store thousands of secrets in Secrets Manager, searching secrets by name may not return enough information about the secrets\. However, using additional search attributes when you search provides in depth information about the secrets\. You can apply multiple filters to the search criteria\. 

You can search for a secret using these attributes:
+ **Name** \- a matching prefix case\-sensitive phrase query using part or all of the secret name to return relevant results\. 

  For example, searching for **MySecret** returns results if you named the secret, MySecret\. The search does not find secrets named mysecret or other variations\. 
+ **Description** \- a matching phrase case\-insensitive query to specify a phrase located in the description to filter matching descriptions\.

  For example, searching for **MyDescription** returns results with MyDescriptiion, mydescription, Mydescription, or myDescription\.
+ **Tag keys** \- a matching prefix case\-sensitive query that searches for the existence of tag key\. Secrets Manager returns only tags matching the specified prefix\.

  For instance, searching for **MyKey** returns only secrets with a Tag Key value of MyKey, not mykey or other variations\.
+ **Tag values** a matching prefix case\-sensitive query that searches for the existence of a tag value\. Secrets Manager returns only tags matching the specified prefix\. 

  For instance, searching for **MyValue** returns only secrets with a Tag Value of MyValue, not myvalue or other variations\. <a name="proc-search-secret"></a>

**Searching for secrets**  
Use the steps on one of the following tabs:

------
#### [ Using the Secrets Manager console ]<a name="proc-secret-search-console"></a>

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Click in the **Search** field and choose the filter\(s\) to use in your search:
   + **Name**
   + **Description**
   + **Tag Keys**
   + **Tag Value**
**Note**  
You may use multiple filters for your search criteria\.

1. Type your keyword in the **Search** field and press **Enter**\.

1. Secrets Manager returns a list of secrets matching your search criteria\.

For example, searching for secrets with the keyword, **conducts**, in the secret description, returns two secrets: 


| Secret Name | Description | 
| --- | --- | 
|  SecretsManager\-rotation\-lambda  |  Conducts an AWS SecretsManager rotation for RDS MySQL using single user rotation scheme  | 
|  SecretsManager\-rotation\-Developers  |  Conducts an AWS SecretsManager rotation for RDS MySQL using single user rotation scheme  | 

------
#### [ Using the AWS CLI or AWS SDK operations ]<a name="proc-secret-search-api"></a>

You can use the following commands to search for a secret stored in AWS Secrets Manager:
+ **API/SDK:** [https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_ListSecret.html](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_ListSecret.html)
+ **AWS CLI:** [https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/list-secret.html](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/list-secret.html)

**Example**  
The following example of the AWS CLI command searches for secrets with the keyword, **conducts**, in the description\.   

```
$ aws secretsmanager list-secrets --filters '[{"Key":"description", "Values":["conducts"]}]' --query "SecretList[*].{SecretName:Name,Description:Description}"
{
   [
    {
        "Description": "Conducts an AWS SecretsManager rotation for RDS MySQL using single user rotation scheme", 
        "SecretName": "SecretsManager-rotation-lambda"
    }, 
    {
        "Description": "Conducts an AWS SecretsManager rotation for RDS MySQL using single user rotation scheme", 
        "SecretName": "SecretsManager-rotation-Developers"
    }
]
}
```
The query returns two secrets, `SecretsManager-rotation-lambda` and `SecretsManager-rotation-Developers`, that contain the keyword, **conducts** in the description\.

------