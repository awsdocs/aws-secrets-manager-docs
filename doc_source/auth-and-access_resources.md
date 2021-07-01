# Resources<a name="auth-and-access_resources"></a>

In Secrets Manager, you control access to your **secrets**, 

Each secret has a unique Amazon Resource Name \(ARN\) associated with it\. You can control access to a secret by specifying the ARN in the `Resource` element of an IAM permission policy\. The ARN of a secret has the following format:

```
arn:aws:secretsmanager:<region>:<account-id>:secret:optional-path/secret-name-6-random-characters
```