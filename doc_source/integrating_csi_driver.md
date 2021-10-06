# Use Secrets Manager secrets in Amazon Elastic Kubernetes Service<a name="integrating_csi_driver"></a>

To show secrets from Secrets Manager as files mounted in [Amazon EKS](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html) pods, you can use the AWS Secrets and Configuration Provider \(ASCP\) for the [Kubernetes Secrets Store CSI Driver](https://secrets-store-csi-driver.sigs.k8s.io/)\. The ASCP works with Amazon Elastic Kubernetes Service \(Amazon EKS\) 1\.17\+\.

With the ASCP, you can store and manage your secrets in Secrets Manager and then retrieve them through your workloads running on Amazon EKS\. If your secret contains multiple key/value pairs in JSON format, you can choose which ones to mount in Amazon EKS\. The ASCP uses [JMESPath syntax](http://jmespath.org/) to query the key/value pairs in your secret\.

You can use IAM roles and policies to limit access to your secrets to specific Amazon EKS pods in a cluster\. The ASCP retrieves the pod identity and exchanges the identity for an IAM role\. ASCP assumes the IAM role of the pod, and then it can retrieve secrets from Secrets Manager that are authorized for that role\.

If you use Secrets Manager automatic rotation for your secrets, you can also use the Secrets Store CSI Driver rotation reconciler feature to ensure you are retrieving the latest secret from Secrets Manager\. For more information, see [Auto rotation of mounted contents and synced Kubernetes Secrets](https://secrets-store-csi-driver.sigs.k8s.io/topics/secret-auto-rotation.html)\.

For a tutorial about how to use the ASCP, see [Tutorial: Create and mount a secret in an Amazon EKS pod](integrating_csi_driver_tutorial.md)\.

To learn how to integrate Parameter Store with Amazon EKS, see [Use Parameter Store parameters in Amazon Elastic Kubernetes Service](https://docs.aws.amazon.com/systems-manager/latest/userguide/integrating_csi_driver.html)\. 

## Install the ASCP<a name="integrating_csi_driver_install"></a>

The ASCP is available on GitHub in the [secrets\-store\-csi\-provider\-aws](https://github.com/aws/secrets-store-csi-driver-provider-aws) repository\. The repo also contains example YAML files for creating and mounting a secret\. You first install the Kubernetes Secrets Store CSI Driver, and then you install the ASCP\.

**To install the ASCP**

1. To install the Secrets Store CSI Driver, run the following commands\. For full installation instructions, see [Installation](https://secrets-store-csi-driver.sigs.k8s.io/getting-started/installation.html) in the Secrets Store CSI Driver Book\.

   ```
   helm repo add secrets-store-csi-driver https://raw.githubusercontent.com/kubernetes-sigs/secrets-store-csi-driver/master/charts
   helm install -n kube-system csi-secrets-store secrets-store-csi-driver/secrets-store-csi-driver
   ```

1. To install the ASCP, use the YAML file in the GitHub repo deployment directory\.

   ```
   kubectl apply -f https://raw.githubusercontent.com/aws/secrets-store-csi-driver-provider-aws/main/deployment/aws-provider-installer.yaml
   ```

## Step 1: Set up access control<a name="integrating_csi_driver_access"></a>

To grant your Amazon EKS pod access to secrets in Secrets Manager, you first create a policy that limits access to the secrets that the pod needs to access\. The policy must include `secretsmanager:GetSecretValue` and `secretsmanager:DescribeSecret` permission\. Then you create an [IAM role for service account](https://docs.aws.amazon.com/eks/latest/userguide/iam-roles-for-service-accounts.html) and attach the policy to it\.

The ASCP retrieves the pod identity and exchanges it for the IAM role\. ASCP assumes the IAM role of the pod, which gives it access to the secrets you authorized\. Other containers can't access the secrets unless you also associate them with the IAM role\. 

For information about creating policies, see [Attach a permissions policy to an identity](auth-and-access_iam-policies.md)\.

For a tutorial about how to use the ASCP, see [Tutorial: Create and mount a secret in an Amazon EKS pod](integrating_csi_driver_tutorial.md)\.

## Step 2: Mount secrets in Amazon EKS<a name="integrating_csi_driver_mount"></a>

To show secrets in Amazon EKS as though they are files on the filesystem, you create a `SecretProviderClass` YAML file that contains information about your secrets and how to display them in the Amazon EKS pod\. 

The `SecretProviderClass` must be in the same namespace as the Amazon EKS pod it references\. 

For a tutorial about how to use the ASCP, see [Tutorial: Create and mount a secret in an Amazon EKS pod](integrating_csi_driver_tutorial.md)\.

## `SecretProviderClass`<a name="integrating_csi_driver_SecretProviderClass"></a>

The `SecretProviderClass` YAML has the following format:

```
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
   name: <NAME>
spec:
  provider: aws
  parameters:
```

**parameters**  
Contains the details of the mount request\.    
**objects**  
A string containing a YAML declaration of the secrets to be mounted\. We recommend using a YAML multi\-line string or pipe \(\|\) character, as shown in [Example](#integrating_csi_driver_example)\.    
**objectName**  
The name or full ARN of the secret\. If you use the ARN, you can omit `objectType`\. This becomes the file name of the secret in the Amazon EKS pod unless you specify `objectAlias`\.   
**jmesPath **  
\(Optional\) A map of the keys in the secret to the files to be mounted in Amazon EKS\. To use this field, your secret value must be in JSON format\. If you use this field, you must include the subfields `path` and `objectAlias`\.    
**path**  
A key from a key/value pair in the JSON of the secret value\.  
**objectAlias**  
The file name to be mounted in the Amazon EKS pod\.  
**objectType**  
Required if you don't use a Secrets Manager ARN for `objectName`\. Can be either `secretsmanager` or `ssmparameter`\.   
**objectAlias**  
\(Optional\) The file name of the secret in the Amazon EKS pod\. If you don't specify this field, the `objectName` appears as the file name\.  
**objectVersion**  
\(Optional\) The version ID of the secret\. We recommend you don't use this field because you must update it every time you update the secret\. By default the most recent version is used\.   
**objectVersionLabel**  
\(Optional\) The alias for the version\. The default is the most recent version AWSCURRENT\. For more information, see [Version](getting-started.md#term_version)\. 

**region**  
\(Optional\) The AWS Region of the secret\. If you don't use this field, the ASCP looks up the Region from the annotation on the node\. This lookup adds overhead to mount requests, so we recommend that you provide the Region for clusters that use large numbers of pods\.

**pathTranslation**  
\(Optional\) A single substitution character to use if the file name \(either `objectName` or `objectAlias`\) contains the path separator character, such as slash \(/\) on Linux\. If a secret contains the path separator, the ASCP will not be able to create a mounted file with that name\. Instead, you can replace the path separator character with a different character by entering it in this field\. If you don't use this field, the default is underscore \(\_\), so for example, `My/Path/Secret` mounts as `My_Path_Secret`\.   
To prevent character substitution, enter the string `False`\.

### Example<a name="integrating_csi_driver_example"></a>

The following example shows a `SecretProviderClass` that mounts six files in Amazon EKS:

1. A secret specified by full ARN\.

1. The `username` key/value pair from the same secret\.

1. The `password` key/value pair from the same secret\.

1. A secret specified by full ARN\.

1. A secret specified by name\.

1. A specific version of a secret\.

```
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: aws-secrets
spec:
  provider: aws
  parameters:
    objects: |
        - objectName: "arn:aws:secretsmanager:us-east-2:[111122223333]:secret:MySecret-00AACC"
          jmesPath: 
              - path: username
                objectAlias: dbusername
              - path: password
                objectAlias: dbpassword
        - objectName: "arn:aws:secretsmanager:us-east-2:[111122223333]:secret:MySecret2-00AABB"
        - objectName: "MySecret3"
          objectType: "secretsmanager"
        - objectName: "MySecret4"
          objectType: "secretsmanager"
          objectVersionLabel: "AWSCURRENT"
```