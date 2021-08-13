# Policies<a name="auth-and-access_policy-types"></a>

Secrets Manager grants permissions , as in almost all AWS services, by creating and attaching permission policies\. Policies have two basic types:
+ **Identity\-based policies** – Attached directly to a user, group, or role\. The policy specifies the allowed tasks for the attached identity\. The attached user automatically and implicitly becomes the `Principal` of the policy\. You can specify the `Actions` the identity can perform, and the `Resources` the identity can perform actions\. The policies enable you to perform the following actions:
  + Grant access to multiple resources to share with the identity\.
  + Control access to APIs for nonexistent resources, such as the various `Create*` operations\.
  + Grant access to an IAM group to a resource\.
+ **Resource\-based policies** – Attached directly to a resource—in this case, a secret\. The policy specifies access to the secret and the actions the user performs on the secret\. The attached secret automatically and implicitly becomes the `Resource` of the policy\. You can specify the `Principals` accessing the secret and the `Actions` the principals can perform\. The policies enable you to perform the following actions:
  + Grant access to multiple principals \(users or roles\) to a single secret\. Note that you *can't* specify an IAM group as a principal in a resource\-based policy\. Only users and roles can be principals\.
  + Grant access to users or roles in other AWS accounts by specifying the account IDs in the `Principal` element of a policy statement\. If you require a "cross account" access to a secret , then this might be one of the primary reasons to use a resource\-based policy\.

Which type should you use? Although their functionality does overlap a great deal, scenarios exist where one has a distinct advantage over the other, and both should have a place in your security strategy\. While either policy type can work, choose the type that simplifies your maintenance for the policies as people come and go from your organization\.

**Important**  
When an IAM principal in one account accesses a secret in a different account, the secret must be encrypted using a custom AWS KMS customer master key \(CMK\)\. A secret encrypted with the default Secrets Manager CMK for the account can be decrypted only by principals in that account\. A principal from a different account must be granted permission to both the secret and to the custom AWS KMS CMK\.   
As an alternative, you can grant cross\-account access to a user and assume an IAM role in the same account as the secret\. Because the role exists in the same account as the secret, the role can access secrets encrypted with the default AWS KMS key for the account\.
