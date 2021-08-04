# Understanding policy evaluation<a name="determine-acccess_understanding-policy-evaluation"></a>

When authorizing access to a secret, Secrets Manager evaluates the secret policy attached to the secret, and all IAM policies attached to the IAM user or role sending the request\. To do this, Secrets Manager uses a process similar to the one described in [Determining Whether a Request is Allowed or Denied](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html#policy-eval-denyallow) in the *IAM User Guide*\.

For example, assume you have two secrets and three users, all in the same AWS account\. The secrets and users have the following policies:
+ Secret 1 doesn't have a secret policy\.
+ Secret 2 has a secret policy that allows access to the user principals Ana and Carlos\.
+ Ana has no IAM policy that references Secret 1 or Secret 2\.
+ Bob's IAM policy allows all Secrets Manager actions for all secrets\.
+ Carlos' IAM policy denies all Secrets Manager actions for all secrets\.

Secrets Manager determines the following access:
+ Ana can access only Secret 2\. She doesn't have a policy attached to her user, but Secret 2 explicitly grants her access\.
+ Bob can access both Secret 1 and Secret 2 because Bob has an IAM policy that allows access to all secrets in the account, and no explicit "deny" override access\.
+ Carlos can't access Secret 1 or Secret 2 because Secrets Manager denies all actions in his IAM policy\. The explicit deny in Carlos' IAM policy overrides the explicit "allow" in the secret policy attached to Secret 2\.

Secrets Manager adds all policy statements from either the secret or the identity that "allow" access\. Any explicit deny overrides any allow to the overlapping action and resource\.