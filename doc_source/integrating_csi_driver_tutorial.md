# Tutorial: Create and mount an AWS Secrets Manager secret in an Amazon EKS pod<a name="integrating_csi_driver_tutorial"></a>

In this tutorial, you create an example secret in Secrets Manager, and then you mount the secret in an Amazon EKS pod and deploy it\. 

Before you begin, install the ASCP: [Install the ASCP](integrating_csi_driver.md#integrating_csi_driver_install)\.

**To create and mount a secret**

1. Set the AWS Region and the name of your cluster as shell variables so you can use them in bash commands\. For *<REGION>*, enter the AWS Region where your Amazon EKS cluster runs\. For *<CLUSTERNAME>*, enter the name of your cluster\.

   ```
   REGION=<REGION>
   CLUSTERNAME=<CLUSTERNAME>
   ```

1. Create a test secret\. For more information, see [Create and manage secrets with AWS Secrets Manager](managing-secrets.md)\.

   ```
   aws --region "$REGION" secretsmanager  create-secret --name MySecret --secret-string '{"username":"lijuan", "password":"hunter2"}'
   ```

1. Create a resource policy for the pod that limits its access to the secret you created in the previous step\. For *<SECRETARN>*, use the ARN of the secret\. Save the policy ARN in a shell variable\. 

   ```
   POLICY_ARN=$(aws --region "$REGION" --query Policy.Arn --output text iam create-policy --policy-name nginx-deployment-policy --policy-document '{
       "Version": "2012-10-17",
       "Statement": [ {
           "Effect": "Allow",
           "Action": ["secretsmanager:GetSecretValue", "secretsmanager:DescribeSecret"],
           "Resource": ["<SECRETARN>"]
       } ]
   }')
   ```

1. Create an IAM OIDC provider for the cluster if you don't already have one\. For more information, see [Create an IAM OIDC provider for your cluster](https://docs.aws.amazon.com/eks/latest/userguide/enable-iam-roles-for-service-accounts.html)\.

   ```
   eksctl utils associate-iam-oidc-provider --region="$REGION" --cluster="$CLUSTERNAME" --approve # Only run this once
   ```

1. Create the service account the pod uses and associate the resource policy you created in step 3 with that service account\. For this tutorial, for the service account name, you use *nginx\-deployment\-sa*\. For more information, see [Create an IAM role for a service account](https://docs.aws.amazon.com/eks/latest/userguide/create-service-account-iam-policy-and-role.html#create-service-account-iam-role)\.

   ```
   eksctl create iamserviceaccount --name nginx-deployment-sa --region="$REGION" --cluster "$CLUSTERNAME" --attach-policy-arn "$POLICY_ARN" --approve --override-existing-serviceaccounts
   ```

1. Create the `SecretProviderClass` to specify which secret to mount in the pod\. The following command uses `ExampleSecretProviderClass.yaml` in the [ASCP GitHub repo examples](https://github.com/aws/secrets-store-csi-driver-provider-aws/blob/main/examples) directory to mount the secret you created in step 2\. For information about creating your own `SecretProviderClass`, see [`SecretProviderClass`](integrating_csi_driver.md#integrating_csi_driver_SecretProviderClass)\.

   ```
   kubectl apply -f https://raw.githubusercontent.com/aws/secrets-store-csi-driver-provider-aws/main/examples/ExampleSecretProviderClass.yaml
   ```

1. Deploy your pod\. The following command uses `ExampleDeployment.yaml` in the [ASCP GitHub repo examples](https://github.com/aws/secrets-store-csi-driver-provider-aws/blob/main/examples) directory to mount the secret in `/mnt/secrets-store` in the pod\.

   ```
   kubectl apply -f https://raw.githubusercontent.com/aws/secrets-store-csi-driver-provider-aws/main/examples/ExampleDeployment.yaml
   ```

1. To verify the secret has been mounted properly, use the following command and confirm that your secret value appears\.

   ```
   kubectl exec -it $(kubectl get pods | awk '/nginx-deployment/{print $1}' | head -1) cat /mnt/secrets-store/MySecret; echo
   ```

   The secret value appears\. 

   ```
   {"username":"lijuan", "password":"hunter2"}
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