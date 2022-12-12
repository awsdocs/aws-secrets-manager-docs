# Use AWS Secrets Manager secrets in Amazon Elastic Kubernetes Service<a name="integrating_csi_driver"></a>

To show secrets from Secrets Manager as files mounted in [Amazon EKS](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html) pods, you can use the AWS Secrets and Configuration Provider \(ASCP\) for the [Kubernetes Secrets Store CSI Driver](https://secrets-store-csi-driver.sigs.k8s.io/)\. The ASCP works with Amazon Elastic Kubernetes Service \(Amazon EKS\) 1\.17\+\. With the ASCP, you can store and manage your secrets in Secrets Manager and then retrieve them through your workloads running on Amazon EKS\. If your secret contains multiple key/value pairs in JSON format, you can choose which ones to mount in Amazon EKS\. The ASCP uses [JMESPath syntax](http://jmespath.org/) to query the key/value pairs in your secret\. The ASCP also works with [Parameter Store parameters](https://docs.aws.amazon.com/systems-manager/latest/userguide/integrating_csi_driver.html)\.

You use IAM roles and policies to grant access to your secrets to specific Amazon EKS pods in a cluster\.

To describe which files to create in the Amazon EKS pod and which secrets to put in them, you create a [`SecretProviderClass`](integrating_csi_driver_SecretProviderClass.md) YAML file\. The `SecretProviderClass` must be in the same namespace as the Amazon EKS pod it references\. 

If you use a private Amazon EKS cluster, ensure that the VPC that the cluster is in has a Secrets Manager endpoint\. The Secrets Store CSI Driver uses the endpoint to make calls to Secrets Manager\. For information about creating an endpoint in a VPC, see [VPC endpoint](vpc-endpoint-overview.md)\.

If you use Secrets Manager automatic rotation for your secrets, you can also use the Secrets Store CSI Driver rotation reconciler feature to ensure you are retrieving the latest secret from Secrets Manager\. For more information, see [Auto rotation of mounted contents and synced Kubernetes Secrets](https://secrets-store-csi-driver.sigs.k8s.io/topics/secret-auto-rotation.html)\.

For a tutorial about how to use the ASCP, see [Tutorial: Create and mount an AWS Secrets Manager secret in an Amazon EKS pod](integrating_csi_driver_tutorial.md)\.

## Install the ASCP<a name="integrating_csi_driver_install"></a>

The ASCP is available on GitHub in the [secrets\-store\-csi\-provider\-aws](https://github.com/aws/secrets-store-csi-driver-provider-aws) repository\. The repo also contains example YAML files for creating and mounting a secret\. 

**To install the ASCP**
+ To install the Secrets Store CSI Driver and ASCP by using Helm, use the following commands\.

  ```
  helm repo add secrets-store-csi-driver https://kubernetes-sigs.github.io/secrets-store-csi-driver/charts
  helm install -n kube-system csi-secrets-store secrets-store-csi-driver/secrets-store-csi-driver
  
  helm repo add aws-secrets-manager https://aws.github.io/secrets-store-csi-driver-provider-aws
  helm install -n kube-system secrets-provider-aws aws-secrets-manager/secrets-store-csi-driver-provider-aws
  ```

  Alternatively, to install by using the YAML file in the deployment directory, use the following commands\.

  ```
  helm repo add secrets-store-csi-driver https://kubernetes-sigs.github.io/secrets-store-csi-driver/charts
  helm install -n kube-system csi-secrets-store secrets-store-csi-driver/secrets-store-csi-driver
  kubectl apply -f https://raw.githubusercontent.com/aws/secrets-store-csi-driver-provider-aws/main/deployment/aws-provider-installer.yaml
  ```

## Step 1: Set up access control<a name="integrating_csi_driver_access"></a>

To grant your Amazon EKS pod access to secrets in Secrets Manager, you first create a permissions policy that grants `secretsmanager:GetSecretValue` and `secretsmanager:DescribeSecret` permission to the secrets that the pod needs to access\. For example policies, see [Permissions policy examples](auth-and-access_examples.md)\.

Then you create an IAM role for service account and attach the policy to it\. For more information, see [IAM role for service accounts](https://docs.aws.amazon.com/eks/latest/userguide/iam-roles-for-service-accounts.html)\.

The ASCP retrieves the pod identity and exchanges it for the IAM role\. ASCP assumes the IAM role of the pod, which gives it access to the secrets you authorized\. Other containers can't access the secrets unless you also associate them with the IAM role\. 

## Step 2: Identify which secrets to mount<a name="integrating_csi_driver_mount"></a>

To determine which secrets the ASCP mounts in Amazon EKS as files on the filesystem, you create a `SecretProviderClass` YAML file\. The `SecretProviderClass` YAML lists the secrets to mount and the file name to mount them as\. The `SecretProviderClass` must be in the same namespace as the Amazon EKS pod it references\. 

The following examples show how to use `SecretProviderClass` to describe the secrets you want to mount and what to name the files mounted in the Amazon EKS pod\. For more information, see [`SecretProviderClass`](integrating_csi_driver_SecretProviderClass.md)\.

**Topics**
+ [Mount secrets by name or ARN](#integrating_csi_driver_example_1)
+ [Mount key/value pairs from a secret](#integrating_csi_driver_example_2)
+ [Define a failover Region for a multi\-Region secret](#integrating_csi_driver_example_3)
+ [Choose a failover secret to mount](#integrating_csi_driver_example_4)

### Example: Mount secrets by name or ARN<a name="integrating_csi_driver_example_1"></a>

The following example shows a `SecretProviderClass` that mounts three files in Amazon EKS:

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
        - objectName: "arn:aws:secretsmanager:us-east-2:111122223333:secret:MySecret2-d4e5f6"
        - objectName: "MySecret3"
          objectType: "secretsmanager"
        - objectName: "MySecret4"
          objectType: "secretsmanager"
          objectVersionLabel: "AWSCURRENT"
```

### Example: Mount key/value pairs from a secret<a name="integrating_csi_driver_example_2"></a>

The following example shows a `SecretProviderClass` that mounts three files in Amazon EKS:

1. A secret specified by full ARN\.

1. The `username` key/value pair from the same secret\.

1. The `password` key/value pair from the same secret\.

```
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: aws-secrets
spec:
  provider: aws
  parameters:
    objects: |
      - objectName: "arn:aws:secretsmanager:us-east-2:111122223333:secret:MySecret-a1b2c3"
          jmesPath: 
              - path: username
                objectAlias: dbusername
              - path: password
                objectAlias: dbpassword
```

### Example: Define a failover Region for a multi\-Region secret<a name="integrating_csi_driver_example_3"></a>

To provide availability during connectivity outages or for disaster recovery configurations, the ASCP supports an automated failover feature to retrieve secrets from a secondary region\.

The following example shows a `SecretProviderClass` that retrieves a secret that is replicated to multiple Regions\. In this example, the ASCP tries to retrieve the secret from both `us-east-1` and `us-east-2`\. If either Region returns a 4xx error, for example for an authentication issue, the ASCP does not mount either secret\. If the secret is retrieved successfully from `us-east-1`, then the ASCP mounts that secret value\. If the secret is not retrieved successfully from `us-east-1`, but it is retrieved successfully from `us-east-2`, then the ASCP mounts that secret value\.

```
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: aws-secrets
spec:
  provider: aws
  parameters:
    region: us-east-1
    failoverRegion: us-east-2
    objects: |
      - objectName: "MySecret"
```

### Example: Choose a failover secret to mount<a name="integrating_csi_driver_example_4"></a>

The following example shows a `SecretProviderClass` that specifies which secret to mount in case of failover\. The failover secret isn't a replica\. In this example, the ASCP tries to retrieve the two secrets specified by `objectName`\. If either returns a 4xx error, for example for an authentication issue, the ASCP does not mount either secret\. If the secret is retrieved successfully from `us-east-1`, then the ASCP mounts that secret value\. If the secret is not retrieved successfully from `us-east-1`, but it is retrieved successfully from `us-east-2`, then the ASCP mounts that secret value\. The mounted file in Amazon EKS is named `MyMountedSecret`\. 

```
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: aws-secrets
spec:
  provider: aws
  parameters:
    region: us-east-1
    failoverRegion: us-east-2
    objects: |
      - objectName: "arn:aws:secretsmanager:us-east-1:111122223333:secret:MySecret-a1b2c3"
        objectAlias: "MyMountedSecret"
        failoverObject: 
          - objectName: "arn:aws:secretsmanager:us-east-2:111122223333:secret:MyFailoverSecret-d4e5f6"
```

## Troubleshoot<a name="integrating_csi_driver_trouble"></a>

You can view most errors by describing the pod deployment\. 

**To see error messages for your container**

1. Get a list of pod names with the following command\. If you aren't using the default namespace, use `-n <NAMESPACE>`\.

   ```
   kubectl get pods
   ```

1. To describe the pod, in the following command, for *<PODID>* use the pod ID from the pods you found in the previous step\. If you aren't using the default namespace, use `-n <NAMESPACE>`\.

   ```
   kubectl describe pod/<PODID>
   ```

**To see errors for the ASCP**
+ To find more information in the provider logs, in the following command, for *<PODID>* use the ID of the *csi\-secrets\-store\-provider\-aws* pod\.

  ```
  kubectl -n kube-system get pods
  kubectl -n kube-system logs pod/<PODID>
  ```