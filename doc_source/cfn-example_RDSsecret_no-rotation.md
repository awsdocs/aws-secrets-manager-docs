# Create a Secrets Manager secret for an Amazon RDS MySQL DB instance with AWS CloudFormation<a name="cfn-example_RDSsecret_no-rotation"></a>

This example creates a secret and an Amazon RDS MySQL DB instance using the credentials in the secret as the user and password\. Secrets Manager generates a password with 32 characters\. As a security best practice, the database is in an Amazon VPC\. 

For a tutorial to turn on rotation for the secret created in this template, see [Set up single user rotation for AWS Secrets Manager](tutorials_rotation-single.md)\.

For an example with automatic rotation already turned on, see [Create a secret with Amazon RDS credentials with automatic rotation](cfn-example_RDSsecret.md)\.

This example uses the following CloudFormation resources for Secrets Manager:
+ [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-secret.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-secret.html)
+ [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-secrettargetattachment.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-secrettargetattachment.html)

For information about creating resources with AWS CloudFormation, see [Learn template basics](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/gettingstarted.templatebasics.html) in the AWS CloudFormation User Guide\.

## JSON<a name="integrating_cloudformation_examples-1_no-rotation.json"></a>

```
{
    "Description": "This is an example template to demonstrate CloudFormation resources for Secrets Manager",
    "Resources": {
        "TestVPC": {
            "Type": "AWS::EC2::VPC",
            "Properties": {
                "CidrBlock": "10.0.0.0/16",
                "EnableDnsHostnames": true,
                "EnableDnsSupport": true,
                "Tags": [
                    {
                        "Key": "Name",
                        "Value": "SecretsManagerTutorial"
                    }
                ]
            }
        },
        "TestSubnet01": {
            "Type": "AWS::EC2::Subnet",
            "Properties": {
                "CidrBlock": "10.0.96.0/19",
                "AvailabilityZone": {
                    "Fn::Select": [
                        "0",
                        {
                            "Fn::GetAZs": {
                                "Ref": "AWS::Region"
                            }
                        }
                    ]
                },
                "VpcId": {
                    "Ref": "TestVPC"
                }
            }
        },
        "TestSubnet02": {
            "Type": "AWS::EC2::Subnet",
            "Properties": {
                "CidrBlock": "10.0.128.0/19",
                "AvailabilityZone": {
                    "Fn::Select": [
                        "1",
                        {
                            "Fn::GetAZs": {
                                "Ref": "AWS::Region"
                            }
                        }
                    ]
                },
                "VpcId": {
                    "Ref": "TestVPC"
                }
            }
        },
        "SecretsManagerTutorialAdmin": {
            "Type": "AWS::SecretsManager::Secret",
            "Properties": {
                "Description": "AWS RDS admin credentials",
                "GenerateSecretString": {
                    "SecretStringTemplate": "{\"username\": \"admin\"}",
                    "GenerateStringKey": "password",
                    "PasswordLength": 32,
                    "ExcludeCharacters": "/@\"'\\"
                }
            }
        },
        "MyDBInstance": {
            "Type": "AWS::RDS::DBInstance",
            "Properties": {
                "AllocatedStorage": 20,
                "DBInstanceClass": "db.t2.micro",
                "DBInstanceIdentifier":"SecretsManagerTutorialDB",
                "Engine": "mysql",
                "DBSubnetGroupName": {
                    "Ref": "MyDBSubnetGroup"
                },
                "MasterUsername": {
                    "Fn::Sub": "{{resolve:secretsmanager:${SecretsManagerTutorialAdmin}::username}}"
                },
                "MasterUserPassword": {
                    "Fn::Sub": "{{resolve:secretsmanager:${SecretsManagerTutorialAdmin}::password}}"
                },
                "BackupRetentionPeriod": 0,
                "VPCSecurityGroups": [
                    {
                        "Fn::GetAtt": [
                            "TestVPC",
                            "DefaultSecurityGroup"
                        ]
                    }
                ]
            }
        },
        "MyDBSubnetGroup": {
            "Type": "AWS::RDS::DBSubnetGroup",
            "Properties": {
                "DBSubnetGroupDescription": "Test Group",
                "SubnetIds": [
                    {
                        "Ref": "TestSubnet01"
                    },
                    {
                        "Ref": "TestSubnet02"
                    }
                ]
            }
        },
        "SecretRDSInstanceAttachment": {
            "Type": "AWS::SecretsManager::SecretTargetAttachment",
            "Properties": {
                "SecretId": {
                    "Ref": "SecretsManagerTutorialAdmin"
                },
                "TargetId": {
                    "Ref": "MyDBInstance"
                },
                "TargetType": "AWS::RDS::DBInstance"
            }
        }
    }
}
```

## YAML<a name="integrating_cloudformation_examples-1_no-rotation.yaml"></a>

```
Description: >-
  This is an example template to demonstrate CloudFormation resources for
  Secrets Manager
Resources:
  TestVPC:
    Type: 'AWS::EC2::VPC'
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      EnableDnsSupport: true
      Tags:
        - Key: Name
          Value: SecretsManagerTutorial
  TestSubnet01:
    Type: 'AWS::EC2::Subnet'
    Properties:
      CidrBlock: 10.0.96.0/19
      AvailabilityZone: !Select 
        - '0'
        - !GetAZs 
          Ref: 'AWS::Region'
      VpcId: !Ref TestVPC
  TestSubnet02:
    Type: 'AWS::EC2::Subnet'
    Properties:
      CidrBlock: 10.0.128.0/19
      AvailabilityZone: !Select 
        - '1'
        - !GetAZs 
          Ref: 'AWS::Region'
      VpcId: !Ref TestVPC
  SecretsManagerTutorialAdmin:
    Type: 'AWS::SecretsManager::Secret'
    Properties:
      Description: AWS RDS admin credentials
      GenerateSecretString:
        SecretStringTemplate: '{"username": "admin"}'
        GenerateStringKey: password
        PasswordLength: 32
        ExcludeCharacters: '"@/\'
  MyDBInstance:
    Type: 'AWS::RDS::DBInstance'
    Properties:
      AllocatedStorage: 20
      DBInstanceClass: db.t2.micro
      DBInstanceIdentifier: SecretsManagerTutorialDB
      Engine: mysql
      DBSubnetGroupName: !Ref MyDBSubnetGroup
      MasterUsername: !Sub '{{resolve:secretsmanager:${SecretsManagerTutorialAdmin}::username}}'
      MasterUserPassword: !Sub '{{resolve:secretsmanager:${SecretsManagerTutorialAdmin}::password}}'
      BackupRetentionPeriod: 0
      VPCSecurityGroups:
        - !GetAtt 
          - TestVPC
          - DefaultSecurityGroup
  MyDBSubnetGroup:
    Type: 'AWS::RDS::DBSubnetGroup'
    Properties:
      DBSubnetGroupDescription: Test Group
      SubnetIds:
        - !Ref TestSubnet01
        - !Ref TestSubnet02
  SecretRDSInstanceAttachment:
    Type: 'AWS::SecretsManager::SecretTargetAttachment'
    Properties:
      SecretId: !Ref SecretsManagerTutorialAdmin
      TargetId: !Ref MyDBInstance
      TargetType: 'AWS::RDS::DBInstance'
```