# Set up SDK permissions<a name="su-sdk-permissions"></a>

To use Amazon Lookout for Vision SDK operations, you need access permissions to the Lookout for Vision API and the Amazon S3 bucket used for model training\.

**Topics**
+ [Granting SDK operation permissions](#su-sdk-managed-policies)
+ [Granting Amazon S3 Bucket permissions](#su-sdk-bucket-permissions)
+ [Assigning permissions](#su-sdk-assign-permissions)

## Granting SDK operation permissions<a name="su-sdk-managed-policies"></a>

We recommend that you grant only the permissions required to perform a task \(least\-privilege permissions\)\. For example, to call [DetectAnomalies](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_DetectAnomalies.html), you need permission to perform `lookoutvision:DetectAnomalies`\. To find the permissions for an operation, check the [API reference](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/Welcome.html)\. 

When you are just starting out with an application, you might not know the specific permissions you need, so you can start with broader permissions\. AWS managed policies provide permissions to help you get started\. 
+ [AmazonLookoutVisionFullAccess](security-iam-awsmanpol.md#security-iam-awsmanpol-AmazonLookoutVisionFullAccess) — allows full access to Amazon Lookout for Vision SDK operations\.
+ [AmazonLookoutVisionReadOnlyAccess](security-iam-awsmanpol.md#security-iam-awsmanpol-AmazonLookoutVisionReadOnlyAccess)`` — allows access to the read\-only SDK operations\.

The managed policies for the console also provide access permissions for SDK operations\. For more information, see [Step 2: Set up permissions](su-setup-permissions.md)\.

For information about AWS managed policies, see [AWS managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies)\.

When you know the permissions that your application needs, reduce permissions further by defining customer managed policies specific to your use cases\. For more information, see [Customer managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#customer-managed-policies)\. 

**Note**  
The getting started instructions require `s3:PutObject` permissions\. For more information, see [Step 1: Create the manifest file and upload images](getting-started.md#getting-started-prepare-files)\.

To assign permissions, see [Assigning permissions](#su-sdk-assign-permissions)\.

## Granting Amazon S3 Bucket permissions<a name="su-sdk-bucket-permissions"></a>

To train a model, you need an Amazon S3 bucket with appropriate permissions to store the images, manifest files and training output\. The bucket must be owned by your AWS account and must be located in the AWS Region in which you are using Amazon Lookout for Vision\. 

The SDK\-only managed policies \(`AmazonLookoutVisionFullAccess` and `AmazonLookoutVisionReadOnlyAccess`\) don't include Amazon S3 bucket permissions and you need to apply the following permission policy to access the buckets you use, including existing console buckets\.

The console managed policies \(`AmazonLookoutVisionConsoleFullAccess` and `AmazonLookoutVisionConsoleReadOnlyAccess`\) include access permissions to the console bucket\. If you are accessing the console bucket with SDK operations and have console managed policy permissions, you don't need to use the following policy\. For more information, see [Step 2: Set up permissions](su-setup-permissions.md)\.  

 

### Deciding task permissions<a name="su-sdk-permissions-tasks"></a>

Use the following information to decide which permissions are needed for the tasks you want to do\. 

#### Creating a dataset<a name="su-sdk-permissions-create-dataset"></a>

To create a dataset with [CreateDataset](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_CreateDataset), you need the following permissions\.
+ `s3:GetBucketLocation` — allows Lookout for Vision to validate that your bucket is in the same region in which you are using Lookout for Vision\.
+ `s3:GetObject` — Allows access to the manifest file specifed in the `DatasetSource` input parameter\. If you want to specify an exact S3 object version of the manifest file, you also need `s3:GetObjectVersion` on the manifest file\. For more information, see [Using versioning in S3 buckets](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Versioning.html)\. 

#### Creating a model<a name="su-sdk-permissions-create-model"></a>

To create a model with [CreateModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_CreateModel), you need the following permissions\.
+ `s3:GetBucketLocation` — allows Lookout for Vision to validate that your bucket is in the same region in which you are using Lookout for Vision\.
+ `s3:GetObject` — allows access to the images specified in the project’s training and test datasets\.
+ `s3:PutObject` — allows permission to store training output in the specified bucket\. You specify the output bucket location in the `OutputConfig` parameter\. Optionally, you can scope permissions down to only object keys specified in the `Prefix` field of the `S3Location` input field\. For more information, see [OutputConfig](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_OutputConfig.html)\.

#### Accessing images, manifest files, and training output<a name="su-sdk-permissions-read-bucket"></a>

Amazon S3 bucket permissions aren't required to view Amazon Lookout for Vision operation responses\. You do need `s3:GetObject` permission if you want to access images, manifests files, and training output referenced in operation responses\. If you are accessing a versioned Amazon S3 object, you need `s3:GetObjectVersion` permission\. 

### Setting Amazon S3 bucket policy<a name="su-sdk-bucket-policy"></a>

You can use the following policy to specify the Amazon S3 bucket permissions needed to create a dataset \(`CreateDataset`\), create a model \(`CreateModel`\), and access images, manifest files, and training output\. Change the value of *my\-bucket* to the name of the bucket that you want use\.

You can adjust the policy to your needs\. For more information, see [Deciding task permissions](#su-sdk-permissions-tasks)\. Add the policy to the desired user\. For more information, see [Creating IAM Policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "LookoutVisionS3BucketAccess",
            "Effect": "Allow",
            "Action": "s3:GetBucketLocation",
            "Resource": [
                "arn:aws:s3:::my-bucket"
            ],
            "Condition": {
                "Bool": {
                    "aws:ViaAWSService": "true"
                }
            }
        },
        {
            "Sid": "LookoutVisionS3ObjectAccess",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:GetObjectVersion",
                "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3:::my-bucket/*"
            ],
            "Condition": {
                "Bool": {
                    "aws:ViaAWSService": "true"
                }
            }
        }
    ]
}
```

To assign permissions, see [Assigning permissions](#su-sdk-assign-permissions)\.

## Assigning permissions<a name="su-sdk-assign-permissions"></a>

To provide access, add permissions to your users, groups, or roles:
+ Users and groups in AWS IAM Identity Center \(successor to AWS Single Sign\-On\):

  Create a permission set\. Follow the instructions in [Create a permission set](https://docs.aws.amazon.com/singlesignon/latest/userguide/howtocreatepermissionset.html) in the *AWS IAM Identity Center \(successor to AWS Single Sign\-On\) User Guide*\.
+ Users managed in IAM through an identity provider:

  Create a role for identity federation\. Follow the instructions in [Creating a role for a third\-party identity provider \(federation\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-idp.html) in the *IAM User Guide*\.
+ IAM users:
  + Create a role that your user can assume\. Follow the instructions in [Creating a role for an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.
  + \(Not recommended\) Attach a policy directly to a user or add a user to a user group\. Follow the instructions in [Adding permissions to a user \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html#users_change_permissions-add-console) in the *IAM User Guide*\.