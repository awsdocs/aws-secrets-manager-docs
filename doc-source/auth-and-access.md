# Authentication and Access Control for AWS Secrets Manager<a name="auth-and-access"></a>

Access to AWS Secrets Manager requires AWS credentials\. Those credentials must have permissions to access the AWS resources that you want to access, such as your Secrets Manager secrets\. The following sections provide details on how you can use AWS Identity and Access Management \(IAM\) policies to help secure access to your secrets and control who can access and administer them\.

Because secrets are, by definition, extremely sensitive information, access to your secrets must be tightly controlled\. By using the permissions capabilities of AWS and IAM permission policies, you can tightly control which users \(or services\) have access to your secrets\. You can specify which API, CLI, and console operations the user can perform on the authorized secrets\. By taking advantage of the granular access features in the [IAM policy language](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html), you can choose to limit the user to only a subset of your secrets—or even to one individual secret—by using tags as filters\. You can also restrict a user to specific versions of a secret by using staging labels as filters\. 

You can also determine who can *manage* which secrets\. You can determine who can update or modify the secrets and their associated metadata\. If you have admin \(all, sometimes called \*/\* meaning "all actions on all resources"\) permissions to the AWS Secrets Manager service, you can delegate access to Secrets Manager tasks by granting permissions to others\.

You can attach permission policies to your users, groups, and roles, and specify the secrets that the attached identities can access\. These are called "identity\-based policies"\. Alternatively, you can attach a permission policy directly to the secret and specify who can access it\. This is called a "resource\-based policy"\. Either way, those policies specify what actions each principal can perform on which secrets\.

For general information about IAM permissions policies, see [Overview of IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html) in the *IAM User Guide*\.

For the permissions that are available specifically for use with AWS Secrets Manager, see [Actions, Resources, and Context Keys You Can Use in an IAM Policy or Secret Policy for AWS Secrets Manager](reference_iam-permissions.md)\.

## <a name="permissions_authorization"></a>

The following sections describe how to manage permissions for AWS Secrets Manager\. We recommend that you read the overview first\.
+ [Overview of Managing Access Permissions to Your Secrets Manager Secrets](auth-and-access_overview.md)
+ [Using Identity\-based Policies \(IAM Policies\) for Secrets Manager](auth-and-access_identity-based-policies.md)
+ [Using Resource\-based Policies for Secrets Manager](auth-and-access_resource-based-policies.md)