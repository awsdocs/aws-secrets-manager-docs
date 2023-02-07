# How AWS Transfer Family uses AWS Secrets Manager secrets<a name="integrating_FTPlong"></a>

AWS Transfer Family is a secure transfer service that enables you to transfer files into and out of AWS storage services\. 

To authenticate Transfer Family users, you can use AWS Secrets Manager as an identity provider\. For more information, see [Working with custom identity providers](https://docs.aws.amazon.com/transfer/latest/userguide/custom-identity-provider-users.html) in the *AWS Transfer Family User Guide* and the blog article [Enable password authentication for AWS Transfer Family using AWS Secrets Manager](http://aws.amazon.com/blogs/storage/enable-password-authentication-for-aws-transfer-family-using-aws-secrets-manager-updated/)\.

You can use Pretty Good Privacy \(PGP\) decryption with the files that Transfer Family processes with workflows\. To use decryption in a workflow step, you provide a PGP key that you manage in Secrets Manager\. For more information, see [Generate and manage PGP keys](https://docs.aws.amazon.com/transfer/latest/userguide/key-management.html#pgp-key-management) in the *AWS Transfer Family User Guide*\.