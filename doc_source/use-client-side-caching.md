# Using the AWS\-developed Open Source Client\-side Caching Components<a name="use-client-side-caching"></a><a name="use-client-side-caching-components"></a>

To efficiently use your secret, you must not simply retrieve the secret value every time you want to use it\. You should also include code to perform the following operations:
+ Your application should cache a secret after retrieving it\. Then, instead of retrieving the secret across the network from Secrets Manager every time you need it, use the cached value\. 
+ Whenever your code receives a network or AWS service error, retry your requests using an algorithm that implements an exponential backoff and retry with jitter, as described at [Exponential Backoff And Jitter](http://aws.amazon.com/blogs/architecture/exponential-backoff-and-jitter/) in the *AWS Architecture Blog*\.

By writing your code to perform those tasks, you get the following benefits:
+ **Reduced costs **– For secrets you use often, retrieving them from the cache reduces the number of API calls your applications make to Secrets Manager\. Since Secrets Manager charges a fee per API call, this can significantly reduce your costs\.
+ **Improved performance** – Retrieving the secret from memory instead of sending a request over the Internet to Secrets Manager can dramatically improve the performance of your applications\.
+ **Improved availability** – Transient network errors can be much less of a problem when you retrieve your secret information from the cache\. 

Secrets Manager has created a client\-side component to implement these best practices and simplify your creation of code to access secrets stored in AWS Secrets Manager\. You install the client and then call the secret, providing the identifier of the secret you want the code to use\. Secrets Manager also requires the AWS credentials you use to call Secrets Manager contain the `secretsmanager:DescribeSecret` and `secretsmanager:GetSecretValue` permissions on the secret\. Those credentials would typically be associated with an IAM role\. The role might be assigned to you at sign\-on time if you use [SAML federation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_saml.html) or [web identity \(OIDC\) federation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_oidc.html)\. If you run your application on an Amazon EC2 instance, then your administrator can assign an IAM role by creating an [instance profile](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/iam-roles-for-amazon-ec2.html)\. 

The component comes in the following forms:
+ Client\-side libraries in Java, Python, Go, and \.NET you interact with the secret instead of directly calling the `GetSecretValue` operation\.
+ A database driver component compliant with Java Database Connectivity \(JDBC\)\. This component is a wrapper around the true JDBC driver that adds the functionality described above\.

For instructions to download and use one of the following links:
+ [Java\-based caching client component](https://github.com/aws/aws-secretsmanager-caching-java )
+ [JDBC\-compatible database connector component](https://github.com/aws/aws-secretsmanager-jdbc )
+ [Python caching client](https://github.com/aws/aws-secretsmanager-caching-python )
+ [Caching client for \.NET](https://github.com/aws/aws-secretsmanager-caching-net )
+ [Go caching client](https://github.com/aws/aws-secretsmanager-caching-go )