# Automating Secret Creation in AWS CloudFormation<a name="integrating_cloudformation"></a>

**Note**  
Currently, AWS Secrets Manager does not support native rotation of secrets for Amazon RS, DocumentDB, and Redshift from CloudFormation in the eu\-south\-1 and af\-south\-1 regions\.

By using an AWS CloudFormation template, you can automate creating secrets for database or service resources in your AWS cloud infrastructure\. 

You can use AWS CloudFormation to automate the creation of your cloud infrastructure\. You create a template in either JSON or YAML to define the resources you need for your project\. AWS CloudFormation then processes the template and builds the defined resources\. This enables you to easily recreate a new copy of your infrastructure whenever you need it\. For example, you can duplicate your test infrastructure to create the public version\. You can also easily share the infrastructure as a simple text file, which enables others to replicate the resources without manual intervention\.

You can use [CloudFormer](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-using-cloudformer.html) to capture all of the details of an existing set of resources into an AWS CloudFormation template\.

Secrets Manager provides the following resource types to enable you to create secrets as part of an AWS CloudFormation template\. For details about configuring each in your AWS CloudFormation template, choose the resource type name for a link to the *AWS CloudFormation User Guide*\.
+ **[ AWS::SecretsManager::Secret ](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-secret.html)** – Creates a secret and stores it in Secrets Manager\. You can specify a password or let Secrets Manager generate one for you\. You may also create an empty secret and update it later using the parameter `SecretString.` 
+ **[ AWS::SecretsManager::ResourcePolicy ](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-resourcepolicy.html)** – Creates a resource\-based policy and attaches it to the specified secret\. A resource\-based policy controls who can perform actions on the secret\.
+ **[ AWS::SecretsManager::RotationSchedule ](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-rotationschedule.html)** – Configures a secret to perform automatic periodic rotation using the specified Lambda rotation function\.
+ **[ AWS::SecretsManager::SecretTargetAttachment ](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-secrettargetattachment.html)** – After you create a secret and then reference it to access the credentials when you create a service or database, this resource type goes back and finishes the configuration of the secret\. Secrets Manager configures the secret with the details about the service or database required for rotation to work\. For example, for an Amazon RDS DB instance, Secrets Manager adds the connection details and database engine type as entries in the `SecureString` property of the secret\.

## Examples<a name="integrating_cloudformation_examples"></a>

The following example templates create a secret and an Amazon RDS MySQL DB instance using the credentials in the secret as the master user and password\. The secret has a resource\-based policy attached that specifies access to the secret\. The template also creates a Lambda rotation function and configures the secret to automatically rotate every 30 days\.

**Note**  
The [JSON specification](https://json.org/) doesn't support comments\. See the [YAML](http://yaml.org/spec/1.2/spec.html) version later on this page for comments\.

**Note**  
If deploying a Aurora PostgreSQL database, you must create a username other than **admin**\. **admin"** is a reserved keyword and you cannot create the database with that username\.

### JSON<a name="integrating_cloudformation_examples-1.json"></a>

```
{
  "MyRDSInstanceRotationSecret": {
    "Type": "AWS::SecretsManager::Secret",
    "Properties": {
      "Description": "A Secrets Manager secret for my RDS DB instance",
      "GenerateSecretString": {
        "SecretStringTemplate": "{\"username\": \"admin\"}",
        "GenerateStringKey": "password",
        "PasswordLength": 16,
        "ExcludeCharacters": "\"@/\\"
      }
    }
  },
  "SecretRDSInstanceAttachment": {
    "Type": "AWS::SecretsManager::SecretTargetAttachment",
    "Properties": {
      "SecretId": {"Ref": "MyRDSInstanceRotationSecret"},
      "TargetId": {"Ref": "MyDBInstance"},
      "TargetType": "AWS::RDS::DBInstance"
    }
  },
  "MyDBInstance": {
    "Type": "AWS::RDS::DBInstance",
    "Properties": {
      "AllocatedStorage": 20,
      "DBInstanceClass": "db.t2.micro",
      "Engine": "mysql",
      "MasterUsername": {"Fn::Join": [
        "",
        ["{{resolve:secretsmanager:", {"Ref": "MyRDSInstanceRotationSecret"}, "::username}}"]
      ]},
      "MasterUserPassword": {"Fn::Join": [
        "",
        ["{{resolve:secretsmanager:", {"Ref": "MyRDSInstanceRotationSecret"}, "::password}}"]
      ]},
      "BackupRetentionPeriod": 0,
      "DBInstanceIdentifier": "rotation-instance"
    }
  },
  "MySecretRotationSchedule": {
    "Type": "AWS::SecretsManager::RotationSchedule",
    "DependsOn": "SecretRDSInstanceAttachment",
    "Properties": {
      "SecretId": {"Ref": "MyRDSInstanceRotationSecret"},
      "RotationLambdaARN": {"Fn::GetAtt": ["MyRotationLambda","Arn"]},
      "RotationRules": {
        "AutomaticallyAfterDays": 30
      }
    }
  },
  "MySecretResourcePolicy": {
    "Type": "AWS::SecretsManager::ResourcePolicy",
    "Properties": {
      "SecretId": {"Ref": "MyRDSInstanceRotationSecret"},
      "ResourcePolicy": {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Deny",
            "Principal": {"AWS": {"Fn::Sub": "arn:aws:iam::${AWS::AccountId}:root"}},
            "Action": "secretsmanager:DeleteSecret",
            "Resource": "*"
          }
        ]
      }
    }
  },
  "LambdaInvokePermission": {
    "Type": "AWS::Lambda::Permission",
    "DependsOn": "MyRotationLambda",
    "Properties": {
      "FunctionName": "cfn-rotation-lambda",
      "Action": "lambda:InvokeFunction",
      "Principal": "secretsmanager.amazonaws.com"
    }
  },
  "MyLambdaExecutionRole": {
    "Type": "AWS::IAM::Role",
    "Properties": {
      "RoleName": "cfn-rotation-lambda-role",
      "AssumeRolePolicyDocument": {
        "Version": "2012-10-17",
        "Statement": [
          {
            "Effect": "Allow",
            "Principal": {"Service": ["lambda.amazonaws.com"]},
            "Action": ["sts:AssumeRole"]
          }
        ]
      },
      "Policies": [
        {
          "PolicyName": "AWSSecretsManagerRotationPolicy",
          "PolicyDocument": {
            "Version": "2012-10-17",
            "Statement": [
              {
                "Effect": "Allow",
                "Action": [
                  "secretsmanager:DescribeSecret",
                  "secretsmanager:GetSecretValue",
                  "secretsmanager:PutSecretValue",
                  "secretsmanager:UpdateSecretVersionStage"
                ],
                "Resource": {"Fn::Sub": "arn:${AWS::Partition}:secretsmanager:${AWS::Region}:${AWS::AccountId}:secret:*"},
                "Condition": {
                  "StringEquals": {
                    "secretsmanager:resource/AllowRotationLambdaArn": {"Fn::Sub": "arn:${AWS::Partition}:lambda:${AWS::Region}:${AWS::AccountId}:function:cfn-rotation-lambda"}
                  }
                }
              },
              {
                "Effect": "Allow",
                "Action": ["secretsmanager:GetRandomPassword"],
                "Resource": "*"
              },
              {
                "Effect": "Allow",
                "Action": [
                  "logs:CreateLogGroup",
                  "logs:CreateLogStream",
                  "logs:PutLogEvents"
                ],
                "Resource": "arn:aws:logs:*:*:*"
              },
              {
                "Effect": "Allow",
                "Action": [
                  "kms:Decrypt",
                  "kms:DescribeKey",
                  "kms:GenerateDataKey"
                ],
                "Resource": "*"
              }
            ]
          }
        }
      ]
    }
  },
  "MyRotationLambda": {
    "Type": "AWS::Lambda::Function",
    "Properties": {
      "Runtime": "python2.7",
      "Role": {"Fn::GetAtt": ["MyLambdaExecutionRole","Arn"]},
      "Handler": "lambda_function.lambda_handler",
      "Description": "This is a Secrets Manager rotation function for a MySQL RDS DB instance",
      "FunctionName": "cfn-rotation-lambda",
      "Environment": {
        "Variables": {
          "SECRETS_MANAGER_ENDPOINT": {"Fn::Sub": "https://secretsmanager.${AWS::Region}.amazonaws.com"}
        }
      },
      "Code": {
        "S3Bucket": "<% replace-this-with-name-of-s3-bucket-that-contains-lambda-function-code %>",
        "S3Key": "<% replace-this-with-path-and-filename-of-zip-file-that-contains-lambda-function-code %>",
        "S3ObjectVersion": "<% replace-this-with-lambda-zip-file-version-if-s3-bucket-versioning-is-enabled %>"
      }
    }
  }
}
```

### YAML<a name="integrating_cloudformation_examples-1.yaml"></a>

```
---
Description: "This is an example template to demonstrate CloudFormation resources for Secrets Manager"
Resources:

  #This is a Secret resource with a randomly generated password in its SecretString JSON.
  MyRDSInstanceRotationSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: 'This is my rds instance secret'
      GenerateSecretString:
        SecretStringTemplate: '{"username": "admin"}'
        GenerateStringKey: 'password'
        PasswordLength: 16
        ExcludeCharacters: '"@/\'
      Tags:
        -
          Key: AppName
          Value: MyApp

  #This is a RDS instance resource. Its master username and password use dynamic references to resolve values from
  #SecretsManager. The dynamic reference guarantees that CloudFormation will not log or persist the resolved value
  #We use a ref to the Secret resource logical id in order to construct the dynamic reference, since the Secret name is being
  #generated by CloudFormation
  MyDBInstance:
    Type: AWS::RDS::DBInstance
    Properties:
      AllocatedStorage: 20
      DBInstanceClass: db.t2.micro
      Engine: mysql
      MasterUsername: !Join ['', ['{{resolve:secretsmanager:', !Ref MyRDSInstanceRotationSecret, ':SecretString:username}}' ]]
      MasterUserPassword: !Join ['', ['{{resolve:secretsmanager:', !Ref MyRDSInstanceRotationSecret, ':SecretString:password}}' ]]
      BackupRetentionPeriod: 0
      DBInstanceIdentifier: 'rotation-instance'

  #This is a SecretTargetAttachment resource which updates the referenced Secret resource with properties about
  #the referenced RDS instance
  SecretRDSInstanceAttachment:
    Type: AWS::SecretsManager::SecretTargetAttachment
    Properties:
      SecretId: !Ref MyRDSInstanceRotationSecret
      TargetId: !Ref MyDBInstance
      TargetType: AWS::RDS::DBInstance

  #This is a RotationSchedule resource. It configures rotation of password for the referenced secret using a rotation lambda
  #The first rotation happens at resource creation time, with subsequent rotations scheduled according to the rotation rules
  #We explicitly depend on the SecretTargetAttachment resource being created to ensure that the secret contains all the
  #information necessary for rotation to succeed
  MySecretRotationSchedule:
    Type: AWS::SecretsManager::RotationSchedule
    DependsOn: SecretRDSInstanceAttachment
    Properties:
      SecretId: !Ref MyRDSInstanceRotationSecret
      RotationLambdaARN: !GetAtt MyRotationLambda.Arn
      RotationRules:
        AutomaticallyAfterDays: 30

  #This is ResourcePolicy resource which can be used to attach a resource policy to the referenced secret.
  #The resource policy in this example denies the DeleteSecret action to all principals within the current account
  MySecretResourcePolicy:
    Type: AWS::SecretsManager::ResourcePolicy
    Properties:
      SecretId: !Ref MyRDSInstanceRotationSecret
      ResourcePolicy: 
        Version: '2012-10-17'
        Statement:
          - Effect: Deny
            Principal: 
              AWS: !Sub 'arn:aws:iam::${AWS::AccountId}:root'
            Action: secretsmanager:DeleteSecret
            Resource: "*"

  #This is a lambda Function resource. We will use this lambda to rotate secrets
  #For details about rotation lambdas, see https://docs.aws.amazon.com/secretsmanager/latest/userguide/rotating-secrets.html
  #The below example assumes that the lambda code has been uploaded to a S3 bucket, and that it will rotate a mysql database password
  MyRotationLambda:
    Type: AWS::Lambda::Function
    Properties:
      Runtime: python2.7
      Role: !GetAtt MyLambdaExecutionRole.Arn
      Handler: lambda_function.lambda_handler
      Description: 'This is a lambda to rotate MySql user passwd'
      FunctionName: 'cfn-rotation-lambda'
      Environment:
        Variables:
          SECRETS_MANAGER_ENDPOINT: !Sub 'https://secretsmanager.${AWS::Region}.amazonaws.com'
      Code:
        S3Bucket: <% name-of-s3-bucket-that-contains-lambda-function-code %>
        S3Key: <% path-and-filename-of-zip-file-that-contains-lambda-function-code %>
        S3ObjectVersion: <% lambda-zip-file-version-if-s3-bucket-versioning-is-enabled %>

  #This is a lambda Permission resource which grants Secrets Manager permission to invoke the rotation lambda function
  LambdaInvokePermission:
    Type: AWS::Lambda::Permission
    DependsOn: MyRotationLambda
    Properties:
      FunctionName: 'cfn-rotation-lambda'
      Action: 'lambda:InvokeFunction'
      Principal: secretsmanager.amazonaws.com

  #This is the IAM Role resource for the rotation lambda, it grants permissions to the lambda to get and update the secret as part of the
  #rotation process. This includes required KMS permissions. It also includes permissions needed for logging to CloudWatch.
  MyLambdaExecutionRole:
    Type: AWS::IAM::Role
    Properties:
      RoleName: 'cfn-rotation-lambda-role'
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          -
            Effect: "Allow"
            Principal:
              Service:
                - "lambda.amazonaws.com"
            Action:
              - "sts:AssumeRole"
      Policies:
        -
          PolicyName: "AWSSecretsManagerRotationPolicy"
          PolicyDocument:
            Version: "2012-10-17"
            Statement:
              -
                Effect: "Allow"
                Action:
                  - "secretsmanager:DescribeSecret"
                  - "secretsmanager:GetSecretValue"
                  - "secretsmanager:PutSecretValue"
                  - "secretsmanager:UpdateSecretVersionStage"
                Resource: !Sub 'arn:${AWS::Partition}:secretsmanager:${AWS::Region}:${AWS::AccountId}:secret:*'
                Condition:
                  StringEquals:
                    secretsmanager:resource/AllowRotationLambdaArn: !Sub 'arn:${AWS::Partition}:lambda:${AWS::Region}:${AWS::AccountId}:function:cfn-rotation-lambda'
              -
                Effect: "Allow"
                Action:
                  - "secretsmanager:GetRandomPassword"
                Resource: "*"
              -
                Effect: "Allow"
                Action:
                  - "logs:CreateLogGroup"
                  - "logs:CreateLogStream"
                  - "logs:PutLogEvents"
                Resource: "arn:aws:logs:*:*:*"
              -
                Effect: "Allow"
                Action:
                  - "kms:Decrypt"
                  - "kms:DescribeKey"
                  - "kms:GenerateDataKey"
                Resource: "*"
```