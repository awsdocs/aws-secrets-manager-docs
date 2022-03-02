# Create a Secrets Manager secret with AWS CloudFormation<a name="cfn-example_secret"></a>

This example creates a secret named **CloudFormationCreatedSecret\-*a1b2c3d4e5f6***\. The secret value is the following JSON, with a 32\-character password that is generated when the secret is created\.

```
{
  "password": "EXAMPLE-PASSWORD",
  "username": "saanvi"
}
```

For information about creating resources with AWS CloudFormation, see [Learn template basics](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/gettingstarted.templatebasics.html) in the AWS CloudFormation User Guide\.

## JSON<a name="cfn-example_secret.json"></a>

```
{
    "Resources": {
        "CloudFormationCreatedSecret": {
            "Type": "AWS::SecretsManager::Secret",
            "Properties": {
                "Description": "Simple secret created by AWS CloudFormation.",
                "GenerateSecretString": {
                    "SecretStringTemplate": "{\"username\": \"saanvi\"}",
                    "GenerateStringKey": "password",
                    "PasswordLength": 32
                }
            }
        }
    }
}
```

## YAML<a name="cfn-example_secret.yaml"></a>

```
Resources:
  CloudFormationCreatedSecret:
    Type: 'AWS::SecretsManager::Secret'
    Properties:
      Description: Simple secret created by AWS CloudFormation.
      GenerateSecretString:
        SecretStringTemplate: '{"username": "saanvi"}'
        GenerateStringKey: password
        PasswordLength: 32
```