# Promoting a replica secret to a standalone secret<a name="standalone-secret"></a>

You may want to promote a replica secret to a standalone secret in the following scenarios: 
+ Implementing disaster recovery \- You can promote a replica secret to a standalone instance as a disaster recovery solution if the primary secret becomes unavailable\.
+ Enabling secret rotation \- If you decide that you want to enable rotation, promote the secret to a standalone secret and configure rotation\. 

For any other changes, to a replica secret, promote it to a standalone secret and add the changes\.

Attempting to change any of the replica secret parameters generates an error message indicating failure to update the secret\. You must promote the replica secret before changing any parameters\.

To promote a replica secret, choose **Promote to standalone secret**\. 

Promoting a replica secret severs the relationship of the replica secret to the primary secret\. Any changes to the primary secret no longer replicate to the standalone secret\. 

**Note**  
Be sure to update the corresponding applications to use the standalone secret\.