# Granting full Secrets Manager administrator permissions to a user<a name="permissions_grant-admin-actions"></a>

To be a Secrets Manager administrator, you must have permissions in several services\. We recommended you don't enable Secrets Manager as an end\-user service enabling your users to create and manage their secrets\. The permissions required to enable rotation grant significant permissions standard users shouldn't have\. Instead, trusted administrators manage the Secrets Manager service\. The end user no longer handles the credentials directly or embed them in code\.

**Warning**  
When you enable rotation, Secrets Manager creates a Lambda function and attaches an IAM role to the function\. This requires several IAM permissions granted only to trusted individuals\. Therefore, the managed policy for Secrets Manager *does not* include these IAM permissions\. Instead, you must explicitly choose to assign the `IAMFullAccess` managed policy, in addition to the `SecretsManagerReadWrite` managed policy to create a full Secrets Manager administrator\.  
Granting access with only the `SecretsManagerReadWrite` policy enables an IAM user to create and manage secrets, but the user can't create and configure the Lambda rotation functions required to enable rotation\.

Complete the following steps to grant full Secrets Manager administrator permissions to an IAM user, group, or role in your account\. In this example, you don't create a new policy\. Instead, you attach an AWS managed policy preconfigured with the permissions\.

**To grant full administrator permissions to an IAM user, group, or role**

1. Sign in to the Identity and Access Management \(IAM\) console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/) as a user with permissions to attach IAM policies to other IAM users\.

   In the IAM console, navigate to **Policies**\.

1. For **Filter Policies: Policy type**, choose **AWS managed**, and then in the **Search** box, start typing **SecretsManagerReadWrite** until you can see the policy in the list\.

1. Choose the **SecretsManagerReadWrite** policy name\.

1. Choose the **Policy actions** , and then choose **Attach**\.

1. Check the box next to the users, groups, or roles you want to add as a Secrets Manager administrator\.

1. Choose **Attach policy**\.

1. Repeat steps 1 through 6 to also attach the **IAMFullAccess** policy\.

The selected users, groups, and roles can immediately begin performing tasks in Secrets Manager\.