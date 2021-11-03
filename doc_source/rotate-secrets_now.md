# Rotate a secret immediately<a name="rotate-secrets_now"></a>

You can only rotate a secret that has automatic rotation turned on\. Turn on automatic rotation for:
+ [Amazon RDS, Amazon DocumentDB, or Amazon Redshift secret](rotate-secrets_turn-on-for-db.md)
+ [Other type of secret](rotate-secrets_turn-on-for-other.md)

**To rotate a secret immediately \(console\)**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose your secret\.

1. On the secret details page, under **Rotation configuration**, choose **Rotate secret immediately**\. 

1. In the **Rotate secret** dialog box, choose **Rotate**\.

## AWS SDK and AWS CLI<a name="rotate-secrets_now_cli"></a>

To rotate a secret immediately, see:
+ **API/SDK:** [RotateSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RotateSecret.html)
+ **AWS CLI:** [rotate\-secret](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/rotate-secret.html)