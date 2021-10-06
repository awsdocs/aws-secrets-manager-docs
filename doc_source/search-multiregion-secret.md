# Find multi\-Region secrets<a name="search-multiregion-secret"></a>

You can find multi\-Region secrets using the following search criteria:
+ **Replicated Secrets: Primary Secrets** \- search for all secrets with the parameter **primary** in your Region\.
+ **Replicated Secrets: Replica Secrets** \- search for all replica secrets in a Region\.

  You cannot search across Regions for replica secrets\. For instance, if you log into the console in the **us\-east\-2** region and search for replica secrets, the search only returns replica secrets in **us\-east\-2**\.
+ **Replicated Secrets: Not Replicated Secrets** \- search for secrets without primary or replica labels in a Region\.