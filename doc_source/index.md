# Amazon Lookout for Vision Developer Guide

-----
*****Copyright &copy; Amazon Web Services, Inc. and/or its affiliates. All rights reserved.*****

-----
Amazon's trademarks and trade dress may not be used in 
     connection with any product or service that is not Amazon's, 
     in any manner that is likely to cause confusion among customers, 
     or in any manner that disparages or discredits Amazon. All other 
     trademarks not owned by Amazon are the property of their respective
     owners, who may or may not be affiliated with, connected to, or 
     sponsored by Amazon.

-----
## Contents
+ [What is Amazon Lookout for Vision?](what-is.md)
+ [Setting up Amazon Lookout for Vision](su-set-up.md)
   + [Step 1: Create an AWS account](su-account.md)
   + [Step 2: Create an IAM administrator user and group](su-account-user.md)
   + [Step 3: Set up permissions](su-setup-permissions.md)
   + [Step 4: Create the console bucket](su-create-console-bucket.md)
   + [Step 5: Set up the AWS CLI and AWS SDKs](su-awscli-sdk.md)
   + [Step 6: Set up SDK permissions](su-sdk-permissions.md)
   + [Step 7: (Optional) Using your own AWS Key Management Service key](su-kms-encryption.md)
   + [Step 8: (Optional) Prepare example images](su-prepare-example-images.md)
+ [Getting Started with the Amazon Lookout for Vision console](getting-started.md)
+ [Getting started with the AWS SDK](getting-started-sdk.md)
+ [Using the Amazon Lookout for Vision dashboard](dashboard.md)
+ [Creating an Amazon Lookout for Vision model](model.md)
   + [Creating your project](model-create-project.md)
   + [Preparing images for a dataset](model-prepare-images.md)
   + [Creating your dataset](model-create-dataset.md)
      + [Creating a dataset using images stored on your local computer](create-dataset-computer-upload.md)
      + [Creating a dataset using images stored in an Amazon S3 bucket](create-dataset-s3.md)
      + [Creating a dataset using an Amazon SageMaker Ground Truth manifest file](create-dataset-ground-truth.md)
         + [Creating a manifest file](manifest-files.md)
            + [Defining JSON lines for anomaly classification](manifest-file-classification.md)
         + [Creating a dataset with a manifest file (console)](create-dataset-use-manifest.md)
         + [Creating a dataset with a manifest file (SDK)](create-dataset-sdk.md)
   + [Editing your dataset](edit-dataset.md)
   + [Training your model](model-train.md)
+ [Improving your model](improve.md)
   + [Viewing performance metrics](performance-metrics.md)
   + [Verifying your model with a trial detection task](trial-detection.md)
+ [Running your trained Amazon Lookout for Vision model](running-model.md)
   + [Starting your Amazon Lookout for Vision model](run-start-model.md)
   + [Stopping your Amazon Lookout for Vision model](run-stop-model.md)
+ [Detecting anomalies in an image](inference-detect-anomalies.md)
+ [Managing your Amazon Lookout for Vision resources](manage.md)
   + [Viewing your projects](view-projects.md)
   + [Deleting a project](delete-project.md)
   + [Creating Amazon Lookout for Vision projects with AWS CloudFormation](creating-projects-with-cloudformation.md)
   + [Viewing your datasets](view-datasets.md)
   + [Deleting a dataset](delete-dataset.md)
   + [Viewing your models](view-models.md)
   + [Deleting a model](delete-model.md)
   + [Tagging models](tagging-model.md)
   + [Viewing your trial detection tasks](view-trial-detections.md)
+ [Security in Amazon Lookout for Vision](security.md)
   + [Data protection in Amazon Lookout for Vision](data-protection.md)
      + [Data encryption](security-data-encryption.md)
      + [Internetwork traffic privacy](security-inter-network-privacy.md)
   + [Identity and access management for Amazon Lookout for Vision](security-iam.md)
      + [How Amazon Lookout for Vision works with IAM](security_iam_service-with-iam.md)
      + [Amazon Lookout for Vision identity-based policy examples](security_iam_id-based-policy-examples.md)
      + [AWS managed policies for Amazon Lookout for Vision](security-iam-awsmanpol.md)
      + [Troubleshooting Amazon Lookout for Vision identity and access](security_iam_troubleshoot.md)
   + [Logging and monitoring in Amazon Lookout for Vision](security-logging-monitoring.md)
      + [Logging Lookout for Vision API calls with AWS CloudTrail](logging-using-cloudtrail.md)
      + [Monitoring Lookout for Vision with Amazon CloudWatch](security-monitoring-cloudwatch.md)
   + [Compliance validation for Amazon Lookout for Vision](lookoutvision-compliance.md)
   + [Amazon Lookout for Vision and interface VPC endpoints (AWS PrivateLink)](vpc-interface-endpoints.md)
   + [Resilience in Amazon Lookout for Vision](disaster-recovery-resiliency.md)
   + [Infrastructure security in Amazon Lookout for Vision](infrastructure-security.md)
+ [Quotas in Amazon Lookout for Vision](limits.md)
+ [Document History for Amazon Lookout for Vision](document-history.md)
+ [AWS glossary](glossary.md)