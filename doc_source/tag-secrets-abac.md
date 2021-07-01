# Implementing attribute\-based access control \(ABAC\) for secrets<a name="tag-secrets-abac"></a>

Attribute\-based access control \(ABAC\) is an authorization strategy that defines permissions based on attributes\. In AWS, these attributes are called tags\. Tags can be attached to IAM principals \(users or roles\) and to AWS resources\. You can create a single ABAC policy or small set of policies for your IAM principals\. These ABAC policies can be designed to allow operations when the principal's tag matches the resource tag\. ABAC is helpful in environments that are growing rapidly and helps with situations where policy management becomes cumbersome\. 

For example, you want to allow access to another account but only for principals with a specific tag\. When you create the roles in the other account, you add tags to the roles to allow access to secrets needed by the roles\.

The following resource policy grants `GetSecretValue` to account `123456789012` only if the tag `access-project` has the same value for the secret and the accessing role:

```
        {
          "Version" : "2012-10-17",
          "Statement" : [ {
             "Effect": "Allow",
             "Principal" : {"AWS": "123456789012"},
             "Condition" : {
                  "StringEquals" : {
                      "aws:ResourceTag/access-project": "${aws:PrincipalTag/access-project}"
                  }
               },
               "Action" : "secretsmanager:GetSecretValue",
               "Resource" : "*"
               } ]
          }
```

You can apply this strategy to simplify management of your access controls\. For more information, see [What is ABAC for AWS?](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_attribute-based-access-control.html) 

For a tutorial on adding this feature to your secrets, see [IAM Tutorial: Define permissions to access AWS resources based on tags\. ](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_attribute-based-access-control.html)