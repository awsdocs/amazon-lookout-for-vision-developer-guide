# Packaging your model \(Console\)<a name="package-job-console"></a>

You can create a model packaging job by using the Amazon Lookout for Vision console\.

For information about package settings, see [Package settings](package-settings.md)\.

**To package a model \(console\)**

1. [Create an Amazon S3 bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/create-bucket-overview.html), or reuse an existing bucket, that Lookout for Vision uses to store the packaging job artifacts \(model component\)\. 

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. Choose **Get started**\. 

1. In the left navigation pane, choose **Projects**\.

1. In the **Projects** section, choose the project that contains the model you want to package\.

1. In the left navigation pane, under the project name, choose **Edge model packages**\.

1. In the **Model packaging jobs** section, choose **Create model packaging job**\.

1. Enter the settings for the package\. For more information, see [Package settings](package-settings.md)\.

1. Choose **Create model packaging job**\.

1. Wait until the packaging job finishes\. The job is finished when the status of the job is **Success**\.

1. Choose the packaging job in the **Model packaging jobs** section\.

1. Choose **Continue deployment in Greengrass** to continue deployment of your model component in AWS IoT Greengrass Version 2\. For more information, see [Deploying your components to a device](device-deploy-components.md)\.