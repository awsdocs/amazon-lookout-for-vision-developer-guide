# AWS managed policies for Amazon Lookout for Vision<a name="security-iam-awsmanpol"></a>







To add permissions to users, groups, and roles, it is easier to use AWS managed policies than to write policies yourself\. It takes time and expertise to [create IAM customer managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create-console.html) that provide your team with only the permissions they need\. To get started quickly, you can use our AWS managed policies\. These policies cover common use cases and are available in your AWS account\. For more information about AWS managed policies, see [AWS managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

AWS services maintain and update AWS managed policies\. You can't change the permissions in AWS managed policies\. Services occasionally add additional permissions to an AWS managed policy to support new features\. This type of update affects all identities \(users, groups, and roles\) where the policy is attached\. Services are most likely to update an AWS managed policy when a new feature is launched or when new operations become available\. Services do not remove permissions from an AWS managed policy, so policy updates won't break your existing permissions\.

Additionally, AWS supports managed policies for job functions that span multiple services\. For example, the `ViewOnlyAccess` AWS managed policy provides read\-only access to many AWS services and resources\. When a service launches a new feature, AWS adds read\-only permissions for new operations and resources\. For a list and descriptions of job function policies, see [AWS managed policies for job functions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html) in the *IAM User Guide*\.









## AWS managed policy: AmazonLookoutVisionReadOnlyAccess<a name="security-iam-awsmanpol-AmazonLookoutVisionReadOnlyAccess"></a>

Use the `AmazonLookoutVisionReadOnlyAccess` policy to allow users read\-only access to Amazon Lookout for Vision \(and its dependencies\) with the following Amazon Lookout for Vision actions \(SDK operations\)\. For example, you can use `DescribeModel` to get information about an existing model\. 
+ [DescribeDataset](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_DescribeDataset)
+ [DescribeModel](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_DescribeModel)
+ [DescribeModelPackagingJob](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_DescribeModelPackagingJob)
+ [DescribeProject](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_DescribeProject)
+ [ListDatasetEntries](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_ListDatasetEntries)
+ [ListModelPackagingJobs](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_ListModels)
+ [ListModels](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_ListModels)
+ [ListProjects](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_ListProjects)
+ [ListTagsForResource](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_ListTagsForResource)

To call read\-only actions, users don't need Amazon S3 bucket permissions\. However, operation responses might include references to Amazon S3 buckets\. For example, the `source-ref` entry in the response from `ListDatasetEntries` is a reference to an image in an Amazon S3 bucket\. Add Amazon S3 bucket permissions if your users need to access referenced buckets\. For example, a user might want to download an image referenced by a `source-ref` field\. For more information, see [Granting Amazon S3 Bucket permissions](su-sdk-permissions.md#su-sdk-bucket-permissions)\. 



You can attach the `AmazonLookoutVisionReadOnlyAccess` policy to your IAM identities\.





**Permissions details**

This policy includes the following permissions\.





```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "LookoutVisionReadOnlyAccess",
            "Effect": "Allow",
            "Action": [
                "lookoutvision:DescribeDataset",
                "lookoutvision:DescribeModel",
                "lookoutvision:DescribeProject",
                "lookoutvision:DescribeModelPackagingJob",
                "lookoutvision:ListDatasetEntries",
                "lookoutvision:ListModels",
                "lookoutvision:ListProjects",
                "lookoutvision:ListTagsForResource",
                "lookoutvision:ListModelPackagingJobs"
            ],
            "Resource": "*"
        }
    ]
}
```

## AWS managed policy: AmazonLookoutVisionFullAccess<a name="security-iam-awsmanpol-AmazonLookoutVisionFullAccess"></a>

Use the `AmazonLookoutVisionFullAccess` policy to allow users full access to Amazon Lookout for Vision \(and its dependencies\) with Amazon Lookout for Vision actions \(SDK operations\)\. For example, you can train a model without having to use the Amazon Lookout for Vision console\. For more information, see [Actions](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_Operations.html)\. 

To create a dataset \(`CreateDataset`\) or create a model \(`CreateModel`\), your users must have full access permissions to the Amazon S3 bucket that stores dataset images, Amazon SageMaker Ground Truth manifest files, and training output\. For more information, see [Step 2: Set up permissions](su-setup-permissions.md)\.

You can also give permission to Amazon Lookout for Vision SDK actions by using the `AmazonLookoutVisionConsoleFullAccess` policy\. 



You can attach the `AmazonLookoutVisionFullAccess` policy to your IAM identities\.





**Permissions details**

This policy includes the following permissions\.





```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "LookoutVisionFullAccess",
            "Effect": "Allow",
            "Action": [
                "lookoutvision:*"
            ],
            "Resource": "*"
        }
    ]
}
```

## AWS managed policy: AmazonLookoutVisionConsoleFullAccess<a name="security-iam-awsmanpol-AmazonLookoutVisionConsoleFullAccess"></a>

Use the `AmazonLookoutVisionFullAccess` policy to allow users full access to the Amazon Lookout for Vision console, actions \(SDK operations\), and any dependencies that the service has\. For more information, see [Getting started with Amazon Lookout for Vision](getting-started.md)\. 

The `LookoutVisionConsoleFullAccess` policy includes permissions to your Amazon Lookout for Vision console bucket\. For information about the console bucket, see [Step 3: Create the console bucket](su-create-console-bucket.md)\. To store datasets, images, and Amazon SageMaker Ground Truth manifest files in a different Amazon S3 bucket, your users need additional permissions\. For more information, see [Setting Amazon S3 bucket permissions](su-setup-permissions.md#su-non-console-buckets)\.



You can attach the `AmazonLookoutVisionConsoleFullAccess` policy to your IAM identities\.





**Permissions groupings**  
This policy is grouped into statements based on the set of permissions provided:
+ `LookoutVisionFullAccess` – Allows access to perform all Lookout for Vision actions\.
+ `LookoutVisionConsoleS3BucketSearchAccess` – Allows listing of all Amazon S3 buckets owned by the caller\. Lookout for Vision uses this action to identify the AWS Region\-specific Lookout for Vision console bucket, if one exists in the caller’s account\.
+ `LookoutVisionConsoleS3BucketFirstUseSetupAccessPermissions` – Allows creating and configuring Amazon S3 buckets that match the Lookout for Vision console bucket name pattern\. Lookout for Vision uses these actions to create and configure a Region\-specific Lookout for Vision console bucket when it can't find one\.
+ `LookoutVisionConsoleS3BucketAccess` – Allows dependent Amazon S3 actions on buckets that match the Lookout for Vision console bucket name pattern\. Lookout for Vision uses `s3:ListBucket` to search for image objects when creating a dataset from an Amazon S3 bucket and when starting a trial detection task\. Lookout for Vision uses `s3:GetBucketLocation ` and `s3:GetBucketVersioning` to validate the bucket's AWS Region, owner, and configuration as part of the following:
  + Creating a dataset
  + Training a model
  + Starting a trial detection task
  + Performing trial detection feedback

  `LookoutVisionConsoleS3ObjectAccess` – Allows reading and writing of Amazon S3 objects inside buckets that match the Lookout for Vision Console bucket name pattern\. Lookout for Vision uses these actions to display images in console gallery views and to upload new images for use in datasets\. Additionally, these permissions allow Lookout for Vision to write out metadata while creating a dataset, training a model, starting a trial detection task, and performing trial detection feedback\.
+ `LookoutVisionConsoleDatasetLabelingToolsAccess` – Allows dependent Amazon SageMaker GroundTruth labeling actions\. Lookout for Vision uses these actions to scan S3 buckets for images, create GroundTruth manifest files, and to annotate trial detection task results with validation labels\.
+ `LookoutVisionConsoleDashboardAccess` \- Allows reading of Amazon CloudWatch metrics\. Lookout for Vision uses these actions to populate the dashboard graphs and anomalies\-detected statistics\.
+ `LookoutVisionConsoleTagSelectorAccess` – Allows reading account\-specific tag key and tag value suggestions\. Lookout for Vision uses these permissions to provide recommendations for tag keys and tag values within the **Manage tags** console pages\.
+ `LookoutVisionConsoleKmsKeySelectorAccess` – Allows listing AWS Key Management Service \(KMS\) keys and aliases\. Amazon Lookout for Vision uses this permission to populate the KMS keys in the suggested **Tags** selection on certain Lookout for Vision actions that support customer managed KMS keys for encryption\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "LookoutVisionFullAccess",
            "Effect": "Allow",
            "Action": [
                "lookoutvision:*"
            ],
            "Resource": "*"
        },
        {
            "Sid": "LookoutVisionConsoleS3BucketSearchAccess",
            "Effect": "Allow",
            "Action": [
                "s3:ListAllMyBuckets"
            ],
            "Resource": "*"
        },
        {
            "Sid": "LookoutVisionConsoleS3BucketFirstUseSetupAccess",
            "Effect": "Allow",
            "Action": [
                "s3:CreateBucket",
                "s3:PutBucketVersioning",
                "s3:PutLifecycleConfiguration",
                "s3:PutEncryptionConfiguration",
                "s3:PutBucketPublicAccessBlock"
            ],
            "Resource": "arn:aws:s3:::lookoutvision-*"
        },
        {
            "Sid": "LookoutVisionConsoleS3BucketAccess",
            "Effect": "Allow",
            "Action": [
                "s3:ListBucket",
                "s3:GetBucketLocation",
                "s3:GetBucketAcl",
                "s3:GetBucketVersioning"
            ],
            "Resource": "arn:aws:s3:::lookoutvision-*"
        },
        {
            "Sid": "LookoutVisionConsoleS3ObjectAccess",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:GetObjectVersion",
                "s3:PutObject",
                "s3:AbortMultipartUpload",
                "s3:ListMultipartUploadParts"
            ],
            "Resource": "arn:aws:s3:::lookoutvision-*/*"
        },
        {
            "Sid": "LookoutVisionConsoleDatasetLabelingToolsAccess",
            "Effect": "Allow",
            "Action": [
                "groundtruthlabeling:RunGenerateManifestByCrawlingJob",
                "groundtruthlabeling:AssociatePatchToManifestJob",
                "groundtruthlabeling:DescribeConsoleJob"
            ],
            "Resource": "*"
        },
        {
            "Sid": "LookoutVisionConsoleDashboardAccess",
            "Effect": "Allow",
            "Action": [
                "cloudwatch:GetMetricData",
                "cloudwatch:GetMetricStatistics"
            ],
            "Resource": "*"
        },
        {
            "Sid": "LookoutVisionConsoleTagSelectorAccess",
            "Effect": "Allow",
            "Action": [
                "tag:GetTagKeys",
                "tag:GetTagValues"
            ],
            "Resource": "*"
        },
        {
            "Sid": "LookoutVisionConsoleKmsKeySelectorAccess",
            "Effect": "Allow",
            "Action": [
                "kms:ListAliases"
            ],
            "Resource": "*"
        }
    ]
}
```

## AWS managed policy: AmazonLookoutVisionConsoleReadOnlyAccess<a name="security-iam-awsmanpol-AmazonLookoutVisionConsoleReadOnlyAccess"></a>

Use the `AmazonLookoutVisionConsoleReadOnlyAccess` policy to allow users read\-only access to the Amazon Lookout for Vision console, actions \(SDK operations\), and any dependencies that the service has\. 

The `AmazonLookoutVisionConsoleReadOnlyAccess` policy includes Amazon S3 permissions for the Amazon Lookout for Vision console bucket\. If your dataset images or Amazon SageMaker Ground Truth manifest files are in a different Amazon S3 bucket, your users need additional permissions\. For more information, see [Setting Amazon S3 bucket permissions](su-setup-permissions.md#su-non-console-buckets)\.



You can attach the `AmazonLookoutVisionConsoleReadOnlyAccess` policy to your IAM identities\.





**Permissions groupings**  
This policy is grouped into statements based on the set of permissions provided:
+ `LookoutVisionReadOnlyAccess` – Allows access to perform read\-only Lookout for Vision actions\.
+ `LookoutVisionConsoleS3BucketSearchAccess` – Allows listing of all S3 buckets owned by the caller\. Lookout for Vision uses this action to identify the AWS Region\-specific Lookout for Vision console bucket, if there is one in the caller’s account\. 
+ `LookoutVisionConsoleS3ObjectReadAccess` – Allows reading Amazon S3 objects and Amazon S3 object versions in Lookout for Vision console buckets\. Lookout for Vision uses these actions to display the images in datasets, models, and trial detections\. 
+ `LookoutVisionConsoleDashboardAccess` – Allows reading Amazon CloudWatch metrics\. Lookout for Vision uses these actions to populate statistics for dashboard graphs and anomalies detected\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "LookoutVisionReadOnlyAccess",
            "Effect": "Allow",
            "Action": [
                "lookoutvision:DescribeDataset",
                "lookoutvision:DescribeModel",
                "lookoutvision:DescribeProject",
                "lookoutvision:DescribeTrialDetection",
                "lookoutvision:DescribeModelPackagingJob",
                "lookoutvision:ListDatasetEntries",
                "lookoutvision:ListModels",
                "lookoutvision:ListProjects",
                "lookoutvision:ListTagsForResource",
                "lookoutvision:ListTrialDetections",
                "lookoutvision:ListModelPackagingJobs"
            ],
            "Resource": "*"
        },
        {
            "Sid": "LookoutVisionConsoleS3BucketSearchAccess",
            "Effect": "Allow",
            "Action": [
                "s3:ListAllMyBuckets"
            ],
            "Resource": "*"
        },
        {
            "Sid": "LookoutVisionConsoleS3ObjectReadAccess",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:GetObjectVersion"
            ],
            "Resource": "arn:aws:s3:::lookoutvision-*/*"
        },
        {
            "Sid": "LookoutVisionConsoleDashboardAccess",
            "Effect": "Allow",
            "Action": [
                "cloudwatch:GetMetricData",
                "cloudwatch:GetMetricStatistics"
            ],
            "Resource": "*"
        }
    ]
}
```





## Lookout for Vision updates to AWS managed policies<a name="security-iam-awsmanpol-updates"></a>



View details about updates to AWS managed policies for Lookout for Vision since this service began tracking these changes\. For automatic alerts about changes to this page, subscribe to the RSS feed on the Lookout for Vision Document history page\.




| Change | Description | Date | Model packaging operations added | Amazon Lookout for Vision added the following model packaging operations to the [AmazonLookoutVisionFullAccess](#security-iam-awsmanpol-AmazonLookoutVisionFullAccess) and [AmazonLookoutVisionConsoleFullAccess](#security-iam-awsmanpol-AmazonLookoutVisionConsoleFullAccess) policies: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/security-iam-awsmanpol.html) Amazon Lookout for Vision added the following model packaging operations to the [AmazonLookoutVisionReadOnlyAccess](#security-iam-awsmanpol-AmazonLookoutVisionReadOnlyAccess) and [AmazonLookoutVisionConsoleReadOnlyAccess](#security-iam-awsmanpol-AmazonLookoutVisionConsoleReadOnlyAccess) policies: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/security-iam-awsmanpol.html)  | December 7th, 2021 | New policies added  | Amazon Lookout for Vision added the following policies\. [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/security-iam-awsmanpol.html)  | May 11th, 2021 | 
| --- | --- | --- | --- | --- | --- | --- | --- | --- | 
|  Lookout for Vision started tracking changes  |  Amazon Lookout for Vision started tracking changes for its AWS managed policies\.  | March 1st, 2021 | 