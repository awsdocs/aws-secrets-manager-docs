# Create an AWS Secrets Manager secret and an Amazon Redshift cluster with AWS CloudFormation<a name="cfn-example_Redshift-secret"></a>

This example creates a secret and an Amazon Redshift cluster using the credentials in the secret as the user and password\. The template also creates a Lambda rotation function from the [Rotation function templates](reference_available-rotation-templates.md) and configures the secret to automatically rotate between 8:00 AM and 10:00 AM UTC on the first day of every month\. As a security best practice, the cluster is in an Amazon VPC\. 

This example uses the following CloudFormation resources for Secrets Manager:
+ [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-secret.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-secret.html)
+ [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-secrettargetattachment.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-secrettargetattachment.html)
+ [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-rotationschedule.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-secretsmanager-rotationschedule.html)

For information about creating resources with AWS CloudFormation, see [Learn template basics](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/gettingstarted.templatebasics.html) in the AWS CloudFormation User Guide\.

## JSON<a name="cfn-example_Redshift-secret.json"></a>

```
{
   "AWSTemplateFormatVersion":"2010-09-09",
   "Transform":"AWS::SecretsManager-2020-07-23",
   "Resources":{
      "TestVPC":{
         "Type":"AWS::EC2::VPC",
         "Properties":{
            "CidrBlock":"10.0.0.0/16",
            "EnableDnsHostnames":true,
            "EnableDnsSupport":true
         }
      },
      "TestSubnet01":{
         "Type":"AWS::EC2::Subnet",
         "Properties":{
            "CidrBlock":"10.0.96.0/19",
            "AvailabilityZone":{
               "Fn::Select":[
                  "0",
                  {
                     "Fn::GetAZs":{
                        "Ref":"AWS::Region"
                     }
                  }
               ]
            },
            "VpcId":{
               "Ref":"TestVPC"
            }
         }
      },
      "TestSubnet02":{
         "Type":"AWS::EC2::Subnet",
         "Properties":{
            "CidrBlock":"10.0.128.0/19",
            "AvailabilityZone":{
               "Fn::Select":[
                  "1",
                  {
                     "Fn::GetAZs":{
                        "Ref":"AWS::Region"
                     }
                  }
               ]
            },
            "VpcId":{
               "Ref":"TestVPC"
            }
         }
      },
      "SecretsManagerVPCEndpoint":{
         "Type":"AWS::EC2::VPCEndpoint",
         "Properties":{
            "SubnetIds":[
               {
                  "Ref":"TestSubnet01"
               },
               {
                  "Ref":"TestSubnet02"
               }
            ],
            "SecurityGroupIds":[
               {
                  "Fn::GetAtt":[
                     "TestVPC",
                     "DefaultSecurityGroup"
                  ]
               }
            ],
            "VpcEndpointType":"Interface",
            "ServiceName":{
               "Fn::Sub":"com.amazonaws.${AWS::Region}.secretsmanager"
            },
            "PrivateDnsEnabled":true,
            "VpcId":{
               "Ref":"TestVPC"
            }
         }
      },
      "MyRedshiftSecret":{
         "Type":"AWS::SecretsManager::Secret",
         "Properties":{
            "Description":"This is my rds instance secret",
            "GenerateSecretString":{
               "SecretStringTemplate":"{\"username\": \"admin\"}",
               "GenerateStringKey":"password",
               "PasswordLength":16,
               "ExcludeCharacters":"\"@/\\"
            },
            "Tags":[
               {
                  "Key":"AppName",
                  "Value":"MyApp"
               }
            ]
         }
      },
      "MyRedshiftCluster":{
         "Type":"AWS::Redshift::Cluster",
         "Properties":{
            "DBName":"myyamldb",
            "NodeType":"ds2.xlarge",
            "ClusterType":"single-node",
            "ClusterSubnetGroupName":{
               "Ref":"ResdshiftSubnetGroup"
            },
            "MasterUsername":{
               "Fn::Sub":"{{resolve:secretsmanager:${MyRedshiftSecret}::username}}"
            },
            "MasterUserPassword":{
               "Fn::Sub":"{{resolve:secretsmanager:${MyRedshiftSecret}::password}}"
            },
            "PubliclyAccessible":false,
            "VpcSecurityGroupIds":[
               {
                  "Fn::GetAtt":[
                     "TestVPC",
                     "DefaultSecurityGroup"
                  ]
               }
            ]
         }
      },
      "ResdshiftSubnetGroup":{
         "Type":"AWS::Redshift::ClusterSubnetGroup",
         "Properties":{
            "Description":"Test Group",
            "SubnetIds":[
               {
                  "Ref":"TestSubnet01"
               },
               {
                  "Ref":"TestSubnet02"
               }
            ]
         }
      },
      "SecretRedshiftAttachment":{
         "Type":"AWS::SecretsManager::SecretTargetAttachment",
         "Properties":{
            "SecretId":{
               "Ref":"MyRedshiftSecret"
            },
            "TargetId":{
               "Ref":"MyRedshiftCluster"
            },
            "TargetType":"AWS::Redshift::Cluster"
         }
      },
      "MySecretRotationSchedule":{
         "Type":"AWS::SecretsManager::RotationSchedule",
         "DependsOn":"SecretRedshiftAttachment",
         "Properties":{
            "SecretId":{
               "Ref":"MyRedshiftSecret"
            },
            "HostedRotationLambda":{
               "RotationType":"RedshiftSingleUser",
               "RotationLambdaName":"SecretsManagerRotationRedshift",
               "VpcSecurityGroupIds":{
                  "Fn::GetAtt":[
                     "TestVPC",
                     "DefaultSecurityGroup"
                  ]
               },
               "VpcSubnetIds":{
                  "Fn::Join":[
                     ",",
                     [
                        {
                           "Ref":"TestSubnet01"
                        },
                        {
                           "Ref":"TestSubnet02"
                        }
                     ]
                  ]
               }
            },
            "RotationRules":{
               "Duration": "2h",
               "ScheduleExpression": "cron(0 8 1 * ? *)"
            }
         }
      }
   }
}
```

## YAML<a name="cfn-example_Redshift-secret.yaml"></a>

```
AWSTemplateFormatVersion: '2010-09-09'
Transform: AWS::SecretsManager-2020-07-23
Resources:
  TestVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      EnableDnsSupport: true
  TestSubnet01:
    Type: AWS::EC2::Subnet
    Properties:
      CidrBlock: 10.0.96.0/19
      AvailabilityZone:
        Fn::Select:
        - '0'
        - Fn::GetAZs:
            Ref: AWS::Region
      VpcId:
        Ref: TestVPC
  TestSubnet02:
    Type: AWS::EC2::Subnet
    Properties:
      CidrBlock: 10.0.128.0/19
      AvailabilityZone:
        Fn::Select:
        - '1'
        - Fn::GetAZs:
            Ref: AWS::Region
      VpcId:
        Ref: TestVPC
  SecretsManagerVPCEndpoint:
    Type: AWS::EC2::VPCEndpoint
    Properties:
      SubnetIds:
      - Ref: TestSubnet01
      - Ref: TestSubnet02
      SecurityGroupIds:
      - Fn::GetAtt:
        - TestVPC
        - DefaultSecurityGroup
      VpcEndpointType: Interface
      ServiceName:
        Fn::Sub: com.amazonaws.${AWS::Region}.secretsmanager
      PrivateDnsEnabled: true
      VpcId:
        Ref: TestVPC
  MyRedshiftSecret:
    Type: AWS::SecretsManager::Secret
    Properties:
      Description: This is my rds instance secret
      GenerateSecretString:
        SecretStringTemplate: '{"username": "admin"}'
        GenerateStringKey: password
        PasswordLength: 16
        ExcludeCharacters: "\"@/\\"
      Tags:
      - Key: AppName
        Value: MyApp
  MyRedshiftCluster:
    Type: AWS::Redshift::Cluster
    Properties:
      DBName: myyamldb
      NodeType: ds2.xlarge
      ClusterType: single-node
      ClusterSubnetGroupName:
        Ref: ResdshiftSubnetGroup
      MasterUsername:
        Fn::Sub: "{{resolve:secretsmanager:${MyRedshiftSecret}::username}}"
      MasterUserPassword:
        Fn::Sub: "{{resolve:secretsmanager:${MyRedshiftSecret}::password}}"
      PubliclyAccessible: false
      VpcSecurityGroupIds:
      - Fn::GetAtt:
        - TestVPC
        - DefaultSecurityGroup
  ResdshiftSubnetGroup:
    Type: AWS::Redshift::ClusterSubnetGroup
    Properties:
      Description: Test Group
      SubnetIds:
      - Ref: TestSubnet01
      - Ref: TestSubnet02
  SecretRedshiftAttachment:
    Type: AWS::SecretsManager::SecretTargetAttachment
    Properties:
      SecretId:
        Ref: MyRedshiftSecret
      TargetId:
        Ref: MyRedshiftCluster
      TargetType: AWS::Redshift::Cluster
  MySecretRotationSchedule:
    Type: AWS::SecretsManager::RotationSchedule
    DependsOn: SecretRedshiftAttachment
    Properties:
      SecretId:
        Ref: MyRedshiftSecret
      HostedRotationLambda:
        RotationType: RedshiftSingleUser
        RotationLambdaName: SecretsManagerRotationRedshift
        VpcSecurityGroupIds:
          Fn::GetAtt:
          - TestVPC
          - DefaultSecurityGroup
        VpcSubnetIds:
          Fn::Join:
          - ","
          - - Ref: TestSubnet01
            - Ref: TestSubnet02
      RotationRules:
        Duration: 2h
        ScheduleExpression: 'cron(0 8 1 * ? *)'
```