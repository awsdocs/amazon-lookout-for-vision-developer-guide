# Amazon Lookout for Vision identity\-based policy examples<a name="security_iam_id-based-policy-examples"></a>

By default, IAM users and roles don't have permission to create or modify Lookout for Vision resources\. They also can't perform tasks using the AWS Management Console, AWS CLI, or AWS API\. An IAM administrator must create IAM policies that grant users and roles permission to perform specific API operations on the specified resources they need\. The administrator must then attach those policies to the IAM users or groups that require those permissions\.

To learn how to create an IAM identity\-based policy using these example JSON policy documents, see [Creating Policies on the JSON Tab](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html#access_policies_create-json-editor) in the *IAM User Guide*\.

**Topics**
+ [Policy best practices](#security_iam_service-with-iam-policy-best-practices)
+ [Accessing a single Amazon Lookout for Vision project](#security_iam_id-based-policy-examples-project)
+ [Tag\-based policy examples](#tag-based-policy-examples)

## Policy best practices<a name="security_iam_service-with-iam-policy-best-practices"></a>

Identity\-based policies determine whether someone can create, access, or delete Lookout for Vision resources in your account\. These actions can incur costs for your AWS account\. When you create or edit identity\-based policies, follow these guidelines and recommendations:
+ **Get started with AWS managed policies and move toward least\-privilege permissions** – To get started granting permissions to your users and workloads, use the *AWS managed policies* that grant permissions for many common use cases\. They are available in your AWS account\. We recommend that you reduce permissions further by defining AWS customer managed policies that are specific to your use cases\. For more information, see [AWS managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) or [AWS managed policies for job functions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html) in the *IAM User Guide*\.
+ **Apply least\-privilege permissions** – When you set permissions with IAM policies, grant only the permissions required to perform a task\. You do this by defining the actions that can be taken on specific resources under specific conditions, also known as *least\-privilege permissions*\. For more information about using IAM to apply permissions, see [ Policies and permissions in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html) in the *IAM User Guide*\.
+ **Use conditions in IAM policies to further restrict access** – You can add a condition to your policies to limit access to actions and resources\. For example, you can write a policy condition to specify that all requests must be sent using SSL\. You can also use conditions to grant access to service actions if they are used through a specific AWS service, such as AWS CloudFormation\. For more information, see [ IAM JSON policy elements: Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html) in the *IAM User Guide*\.
+ **Use IAM Access Analyzer to validate your IAM policies to ensure secure and functional permissions** – IAM Access Analyzer validates new and existing policies so that the policies adhere to the IAM policy language \(JSON\) and IAM best practices\. IAM Access Analyzer provides more than 100 policy checks and actionable recommendations to help you author secure and functional policies\. For more information, see [IAM Access Analyzer policy validation](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-policy-validation.html) in the *IAM User Guide*\.
+ **Require multi\-factor authentication \(MFA\)** – If you have a scenario that requires IAM users or root users in your account, turn on MFA for additional security\. To require MFA when API operations are called, add MFA conditions to your policies\. For more information, see [ Configuring MFA\-protected API access](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa_configure-api-require.html) in the *IAM User Guide*\.

For more information about best practices in IAM, see [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

## Accessing a single Amazon Lookout for Vision project<a name="security_iam_id-based-policy-examples-project"></a>

In this example, you want to grant an IAM user in your AWS account access to one of your Amazon Lookout for Vision projects\.

```
{
    "Sid": "SpecificProjectOnly",
    "Effect": "Allow",
    "Action": [
        "lookoutvision:DetectAnomalies"
    ],
    "Resource": "arn:aws:lookoutvision:us-east-1:123456789012:model/myproject/*"
}
```

## Tag\-based policy examples<a name="tag-based-policy-examples"></a>

Tag\-based policies are JSON policy documents that specify the actions that a principal can perform on tagged resources\. 

### Use a tag to access a resource<a name="tag-resource"></a>

This example policy grants an IAM user or role in your AWS account permission to use the `DetectAnomalies` operation with any model tagged with the key `stage` and the value `production`\. 

```
{ 
    "Version": "2012-10-17", 
    "Statement": [ 
        { 
            "Effect": "Allow", 
            "Action": [ 
                "LookoutVision:DetectAnomalies" 
            ], 
            "Resource": "*", 
            "Condition": {
                "StringEquals": {
                    "aws:ResourceTag/stage": "production" 
                } 
            } 
        } 
   ]
}
```

#### Use a tag to deny access to specific Amazon Lookout for Vision operations<a name="example-tag-operations"></a>

This example policy denies permission for an IAM user or role in your AWS account to call the `DeleteModel` or `StopModel` operations with any model tagged with the key `stage` and the value `production`\. 

```
{ 
    "Version": "2012-10-17", 
    "Statement": [ 
        { 
            "Effect": "Deny", 
            "Action": [ 
                "LookoutVision:DeleteModel",
                "LookoutVision:StopModel" 
            ], 
            "Resource": "*", 
            "Condition": {
                "StringEquals": {
                    "aws:ResourceTag/stage": "production" 
                } 
            } 
        } 
   ]
}
```