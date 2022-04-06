# Rotation strategies<a name="rotating-secrets_strategies"></a>

There are two rotation strategies offered by Secrets Manager: 
+ [Single user rotation strategy](#rotating-secrets-one-user-one-password)
+ [Alternating users rotation strategy](#rotating-secrets-two-users)

## Single user rotation strategy<a name="rotating-secrets-one-user-one-password"></a>

The single user strategy updates credentials for one user in one secret\.

This is the simplest rotation strategy, and it is appropriate for most use cases\. You can use single\-user rotation for:
+ Accessing databases\. Database connections are not dropped when a secret rotates, and new connections after rotation use the new credentials\.
+ Accessing services that allow the user to create one user account, for example with email address as the user name\. The service typically allows the user to change the password as often as required, but the user can't create additional users or change their user name\.
+ Users created as necessary, called *ad\-hoc users*\. 
+ Users who enter their password interactively instead of having an application programmatically retrieve it from Secrets Manager\. This type of user does not expect to have to change their user name as well as password\. 

For detailed instructions, see [Tutorial: Set up single user rotation for AWS Secrets Manager](tutorials_rotation-single.md)\.

While this type of rotation is happening, there is a short period of time between when the password in the database changes and when the corresponding secret updates\. In this time, there is a low risk of the database denying calls that use the rotated credentials\. You can mitigate this risk with an [appropriate retry strategy](http://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/)\.

To use this strategy, the user in your secret must have permission to update their password\. 

**To use the single user rotation strategy**

1. Create a secret with the database or service credentials\.

1. [Turn on automatic rotation](https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotating-secrets.html) for your secret, and for **Select which secret will be used to perform the rotation**, choose **Use this secret / Single user rotation**\.

## Alternating users rotation strategy<a name="rotating-secrets-two-users"></a>

The alternating users strategy updates credentials for two users in one secret\. You create the first user, and rotation clones it to create the second\. Rotation updates the original and the clone credentials in an alternating pattern\. For example, if the first version is `user/password1`, then the second version has `user_clone/password2`\. The third version has `user/password3`, and the fourth version has `user_clone/password4`\. Applications that retrieve the secret from Secrets Manager get the existing version of the credentials while rotation creates the new version\. Once the new version is ready, rotation switches the staging labels so that applications use the new version\. With this strategy, you have two sets of valid credentials at any given time: both `user` and `user_clone` credentials are valid\. 

Because most users don't have permission to clone themselves, Secrets Manager uses a `superuser` to do the cloning\. You provide the credentials for the `superuser` in another secret\. 

For detailed instructions, see [Tutorial: Set up alternating users rotation for AWS Secrets Manager](tutorials_rotation-alternating.md)\.

This strategy is appropriate for:
+ Applications and databases with permission models where one role owns the database tables and a second role for the application has permission to access the tables\.
+ Applications that require high availability\. There is less chance of applications getting a deny during this type of rotation than single user rotation\.

If the database or service is hosted on a server farm where the password change takes time to propagate to all member servers, there is a risk of the database denying calls that use the rotated credentials\. You can mitigate this risk with an [appropriate retry strategy](http://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/)\.

**To use the alternating users strategy**

1. Create a `superuser` with elevated credentials for your database or service\. This user must be able to create new users and change their credentials\.

1. Create a secret for the `superuser` credentials\.

1. Create a `user` who will access your database or service\.

1. Create a secret for the `user` credentials\. 

1. [Turn on automatic rotation](https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotating-secrets.html) for the `user` secret\. For **Use separate credentials**, choose **Yes**, and then under **Secrets**, choose the `superuser` secret\.