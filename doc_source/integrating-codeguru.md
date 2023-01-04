# Find unprotected secrets in your code with Amazon CodeGuru Reviewer<a name="integrating-codeguru"></a>

Amazon CodeGuru Reviewer is a service that uses program analysis and machine learning to detect potential defects that are difficult for developers to find and offers suggestions for improving your Java and Python code\. CodeGuru Reviewer integrates with Secrets Manager to find unprotected secrets in your code\. For the types of secrets it can find, see [Types of secrets detected by CodeGuru Reviewer](https://docs.aws.amazon.com/codeguru/latest/reviewer-ug/recommendations.html#secrets-found-types) in the *Amazon CodeGuru Reviewer User Guide*\.

Once you've found hardcoded secrets, take action to replace them:
+ [Move hardcoded database credentials to AWS Secrets Manager](hardcoded-db-creds.md)
+ [Move hardcoded secrets to AWS Secrets Manager](hardcoded.md)