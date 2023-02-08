# Step 2: Set up permissions<a name="su-setup-permissions"></a>

To use Amazon Lookout for Vision, you needs access permissions to the Lookout for Vision console, AWS SDK operations, and the Amazon S3 bucket that you use for model training\. 

**Note**  
If you only use AWS SDK operations, you can use policies that are scoped to AWS SDK operations\. For more information, see [Set up SDK permissions](su-sdk-permissions.md)\. 

**Topics**
+ [Setting console access with AWS managed policies](#su-console-managed-policies)
+ [Setting Amazon S3 bucket permissions](#su-non-console-buckets)
+ [Assigning permissions](#su-assign-permissions)

## Setting console access with AWS managed policies<a name="su-console-managed-policies"></a>

Use the following AWS managed policies to apply appropriate access permissions for the Amazon Lookout for Vision console and SDK operations\. 
+ [AmazonLookoutVisionConsoleFullAccess](security-iam-awsmanpol.md#security-iam-awsmanpol-AmazonLookoutVisionConsoleFullAccess) — allows full access to the Amazon Lookout for Vision console and SDK operations\. You need `AmazonLookoutVisionConsoleFullAccess` permissions to create the console bucket\. For more information, see [Step 3: Create the console bucket](su-create-console-bucket.md)\.
+ [AmazonLookoutVisionConsoleReadOnlyAccess](security-iam-awsmanpol.md#security-iam-awsmanpol-AmazonLookoutVisionConsoleReadOnlyAccess)— allows read\-only access to the Amazon Lookout for Vision console and SDK operations\.

To assign permissions, see [Assigning permissions](#su-assign-permissions)\.

For information about AWS managed policies, see [AWS managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies)\.

## Setting Amazon S3 bucket permissions<a name="su-non-console-buckets"></a>

Amazon Lookout for Vision uses an Amazon S3 bucket to store the following files: 
+ Dataset images — Images that are used to train a model\. For more information, see [Creating your dataset](model-create-dataset.md)\.
+ Amazon SageMaker Ground Truth format manifest files\. For example, the manifest file output from SageMaker GroundTruth job\. For more information, see [Creating a dataset using an Amazon SageMaker Ground Truth manifest file](create-dataset-ground-truth.md)\.
+ The output from model training\.

If you use the console, Lookout for Vision creates an Amazon S3 bucket \(console bucket\) to manage your projects\. The `LookoutVisionConsoleReadOnlyAccess` and `LookoutVisionConsoleFullAccess` managed policies include Amazon S3 access permissions for the console bucket\.  

You can use the console bucket to store dataset images and SageMaker Ground Truth format manifest files\. Alternatively, You can use a different Amazon S3 bucket\. The bucket must be owned by your AWS account and must be located in the AWS Region in which you are using Lookout for Vision\. 

To use a different bucket, add the following policy to the desired user or group\. Replace `my-bucket` with the name of the desired bucket\. For information about adding IAM policies, see [Creating IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "LookoutVisionS3BucketAccessPermissions",
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:ListBucket"
            ],            
            "Resource": [
                "arn:aws:s3:::my-bucket"
            ]
        },
        {
            "Sid": "LookoutVisionS3ObjectAccessPermissions",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:GetObjectVersion",
                "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3:::my-bucket/*"
            ]
        }
    ]
}
```

To assign permissions, see [Assigning permissions](#su-assign-permissions)\.

## Assigning permissions<a name="su-assign-permissions"></a>

To provide access, add permissions to your users, groups, or roles:
+ Users and groups in AWS IAM Identity Center \(successor to AWS Single Sign\-On\):

  Create a permission set\. Follow the instructions in [Create a permission set](https://docs.aws.amazon.com/singlesignon/latest/userguide/howtocreatepermissionset.html) in the *AWS IAM Identity Center \(successor to AWS Single Sign\-On\) User Guide*\.
+ Users managed in IAM through an identity provider:

  Create a role for identity federation\. Follow the instructions in [Creating a role for a third\-party identity provider \(federation\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-idp.html) in the *IAM User Guide*\.
+ IAM users:
  + Create a role that your user can assume\. Follow the instructions in [Creating a role for an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.
  + \(Not recommended\) Attach a policy directly to a user or add a user to a user group\. Follow the instructions in [Adding permissions to a user \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html#users_change_permissions-add-console) in the *IAM User Guide*\.