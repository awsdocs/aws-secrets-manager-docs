# Managing access to resources with policies<a name="auth-and-access_resource-access"></a>

A *permissions policy* describes *who* can perform *which actions* on *which resources*\. The following section explains the available options for creating permissions policies, and also briefly describes the elements making up a policy, and the two types of policies you can create for Secrets Manager\.

**Note**  
This section discusses using IAM in the context of Secrets Manager, but does not provide detailed information about the IAM service\. For complete IAM documentation, see the [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)\. For information about IAM policy syntax and descriptions, see the [AWS IAM Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

You should understand you use either secret\-based or identity\-based permission policies for Secrets Manager \. Secrets Manager groups together all of the applied policies and processes as one large policy\. This basic rule structure controls the interaction:


|  | 
| --- |
| Explicit Deny >> Explicit Allow >> Implicit Deny \(default\) | 

For a request to perform an AWS operation on an AWS resource, the following rules apply:
+ **If any statement in any policy with an explicit "deny" matches the request action and resource**: The explicit deny overrides everything else and blocks the specified actions on the specified resources \.
+ **If no explicit "deny", but a statement with an explicit "allow" matches the request action and resource**: The explicit allow applies, which grants actions in that statement access to the resources in that statement\.
+ **If no statement with an explicit "deny" and no statement with an explicit "allow" matches the request action and resource**: AWS *implicitly* denies the request by default\.

You should understand that an *implicit* deny can be overridden with an explicit allow\. An *explicit* deny can't be overridden\.


**Effective permissions when multiple policies apply**  

| Policy A | Policy B | Effective Permissions | 
| --- | --- | --- | 
| Allows | Silent | Access allowed | 
| Allows | Allows | Access allowed | 
| Allows | Denies | Access denied | 
| Silent | Silent | Access denied | 
| Silent | Allows | Access allowed | 
| Silent | Denies | Access denied | 
| Denies | Silent | Access denied | 
| Denies | Allows | Access denied | 
| Denies | Denies | Access denied | 

Policy A and Policy B can be of any type: an IAM identity\-based policy attached to a user or role, or a resource\-based policy attached to the secret\. You can include the effect of additional policies by taking the effective permission of the first two policies \(the results\) and treating that like a new Policy A\. Then add the results of the third policy as Policy B, and repeat this for each additional policy that applies\.

**Topics**
+ [AWS managed policies](combining-identity-resource-policies.md)
+ [Specifying policy statement elements](auth-and-access_policy-elements.md)
+ [Identity\-based policies](auth-and-access_iam-policies.md)
+ [Resource\-based policies](auth-and-access_resource-policies.md)