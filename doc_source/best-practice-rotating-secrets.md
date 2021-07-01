# Rotating secrets on a schedule<a name="best-practice-rotating-secrets"></a>

If you don't change your secrets for a long period of time, the secrets become more likely to be compromised\. As more users get access to a secret, it may be possible someone has mishandled and leaked it to an unauthorized entity\. Secrets can be leaked through logs and cache data\. They can be shared for debugging purposes and not changed or revoked once the debugging completes\. For all these reasons, secrets should be rotated frequently\. 

Configure Secrets Manager to rotate your secrets frequently to avoid using the same secrets for your applications\. We recommend that you rotate your secrets every 30 days\. 

See [Rotating your AWS Secrets Manager secrets](rotating-secrets.md)