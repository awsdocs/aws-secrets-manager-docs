# Rotate a secret immediately<a name="rotate-secrets_now"></a>

You can only rotate a secret that has rotation configured\. This can be a secret that currently has automatic rotation turned on, or one that previously had rotation turned on\. Turn on automatic rotation for:
+ [Rotate DB credentials](rotate-secrets_turn-on-for-db.md)
+ [Rotate a secret](rotate-secrets_turn-on-for-other.md)

**To rotate a secret immediately \(console\)**

1. Open the Secrets Manager console at [https://console\.aws\.amazon\.com/secretsmanager/](https://console.aws.amazon.com/secretsmanager/)\.

1. Choose your secret\.

1. On the secret details page, under **Rotation configuration**, choose **Rotate secret immediately**\. 

1. In the **Rotate secret** dialog box, choose **Rotate**\.

## AWS SDK and AWS CLI<a name="rotate-secrets_now_cli"></a>

To rotate a secret immediately, see [rotate\-secret](https://docs.aws.amazon.com/cli/latest/reference/secretsmanager/rotate-secret.html)\.

## AWS SDK<a name="rotate-secrets_now_sdk"></a>

To rotate a secret immediately, use the [RotateSecret](https://docs.aws.amazon.com/secretsmanager/latest/apireference/API_RotateSecret.html) action\. For more information, see [AWS SDKs](asm_access.md#asm-sdks)\.