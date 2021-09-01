# Cross\-account access<a name="auth-and-access_cross-account-role-vs-account"></a>

When you want to use a resource\-based policy attached to a secret give access to an IAM principal in a different AWS account, you have two options:
+ **Specify only the other account ID** – In the `Principal` element of the statement, you specify the Amazon Resource Name \(ARN\) of the "foreign" account root\. This enables the administrator of the foreign account to delegate access to roles in the foreign account\. The administrator must then assign IAM permission policies to the role or roles that require access to the secret\.

  ```
  "Principal": {"AWS": arn:aws:iam::AccountId:root}
  ```
+ **Specify the exact user or role in the other account** – You specify an exact user or role ARN as the `Principal` of a secret\-based policy\. You get the ARN from the administrator of the other account\. Only the specific single user or role in the account can access the resource\.

  ```
  "Principal": [ 
      {"AWS": "arn:aws:iam::AccountId:role/MyCrossAccountRole"},
      {"AWS": "arn:aws:iam::AccountId:user/MyUserName"}
  ]
  ```

As a best practice, we recommend you specify only the account in the secret\-based policy, for the following reasons: 
+ In both cases, you trust the administrator of the other account\. In the first case, you trust the administrator to ensure only authorized individuals have access to the IAM user, or can assume the specified role\. This creates essentially the same level of trust as specifying only the account ID\. You trust the account and the administrator\. When you specify only the account ID, you give the administrator the flexibility to manage users\.
+ When you specify an exact role, IAM internally converts the role to a "principal ID" unique to the role\. If you delete the role and recreate it with the same name, the role receives a new principal ID\. This means the new role doesn't automatically obtain access to the resource\. IAM provides this functionality for security reasons, and means that an accidental delete and restore can result in "broken" access\.

When you grant permissions to only the account root, that set of permissions then becomes the limit of the options the administrator of the account can delegate to users and roles\. The administrator can't grant a permission to the resource if you didn't grant it to the account first\.

**Important**  
If you choose to grant cross\-account access directly to the secret without using a role, then the secret must be encrypted by using a custom AWS KMS customer master key \(CMK\)\. A principal from a different account must be granted permission to both the secret and the custom AWS KMS CMK\. 