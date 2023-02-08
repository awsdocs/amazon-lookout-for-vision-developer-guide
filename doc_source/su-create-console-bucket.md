# Step 3: Create the console bucket<a name="su-create-console-bucket"></a>

To use the Amazon Lookout for Vision console, you need an Amazon S3 bucket that is known as the console bucket\. The console bucket stores the following:
+ Images that you [upload](create-dataset-computer-upload.md) to a dataset with the console\.
+ Training results for [model training](model-train.md#create-model-console) that you start with console\. 
+ [Trial detection](trial-detection.md) results\.
+ Temporary manifest files that the console creates when you use the console to create a dataset by [automatically labeling](create-dataset-s3.md#create-dataset-s3-auto-label) images in an S3 bucket\. The console doesn't delete the manifest files\. 

When you first open the Amazon Lookout for Vision console in a new AWS Region, Lookout for Vision creates the console bucket on your behalf\. Note the console bucket name because you might need to use the bucket name in AWS SDK operations or console tasks, such as creating a dataset\.

Alternatively, you can create the console bucket by using Amazon S3\. Use this approach if Amazon S3 bucket policies don't let the Amazon Lookout for Vision console successfully create the console bucket\. For example, a policy that disallows the automatic creation of an Amazon S3 bucket\. 

**Note**  
If you only use the AWS SDK and not the Lookout for Vision console, you don't need to create the console bucket\. You can use a different S3 bucket with a name of your choosing\.

The format of the console bucket name is `lookoutvision`\-*<region>*\-*<random value>*\. The random value ensures that there isn't a collision between bucket names\.

**Topics**
+ [Creating the console bucket with the Amazon Lookout for Vision console](#su-create-console-bucket-lfv-procedure)
+ [Creating the console bucket with Amazon S3](#su-create-console-bucket-s3-procedure)
+ [Console bucket settings](#su-console-bucket-settings)

## Creating the console bucket with the Amazon Lookout for Vision console<a name="su-create-console-bucket-lfv-procedure"></a>

Use the following procedure to create the console bucket for an AWS Region with the Amazon Lookout for Vision console\. For information about the S3 bucket settings that we enable, see [Console bucket settings](#su-console-bucket-settings)\.

**To create the console bucket by using the Amazon Lookout for Vision console**

1. Ensure that the user or group you are using has `AmazonLookoutVisionConsoleFullAccess` permission\. For more information, see [Step 2: Set up permissions](su-setup-permissions.md)\.

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. On the navigation bar, choose **Select a region**\. Then choose the AWS Region for which you want to create the console bucket\.

1. Choose **Get started**\.

1. If this is the first time that you've opened the console in the current AWS Region, do the following in the **First time set up** dialog box:

   1. Copy down the name of the Amazon S3 bucket that's shown\. You'll need this information later\.

   1. Choose **Create S3 bucket** to let Amazon Lookout for Vision create the console bucket on your behalf\.

   The **First time set up** dialog box is not shown if the console bucket for the current AWS Region already exists\.

1. Close the browser window\.

## Creating the console bucket with Amazon S3<a name="su-create-console-bucket-s3-procedure"></a>

You can use Amazon S3 to create the console bucket\. You must create the bucket with [Amazon S3 versioning](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Versioning.html) enabled\. We recommend that you use an [Amazon S3 lifecycle configuration](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.html) to remove noncurrent \(previous\) versions of an object and delete incomplete multipart uploads\. We don't recommend a lifecycle configuration that deletes current versions of an object\. For information about the S3 bucket settings we enable for console buckets that you create with the Amazon Lookout for Vision console, see [Console bucket settings](#su-console-bucket-settings)\. 

1. Decide the AWS Region in which you want to create a console bucket\. For information about supported regions, see [Amazon Lookout for Vision endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/lookoutvision.html)\.

1. Create a bucket using the S3 console instructions at [Creating a bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/create-bucket-overview.html)\. Do the following:

   1. For step 3 specify a bucket name that is prepended with `lookoutvision-region-your-identifier`\. Change `region` to the region code you chose in the previous step\. Change `your-identifier` to a unique identifier of your choosing\. For example, `lookoutvision-us-east-1-my-console-bucket-1`

   1. For step 4 choose the AWS Region that you want to use\.

1. Enable versioning for the bucket by following the S3 console instructions at [Enabling versioning on buckets](https://docs.aws.amazon.com/AmazonS3/latest/userguide/manage-versioning-examples.html)\. 

1. \(Optional\) Specify a lifecycle configuration for the bucket by following the S3 console instructions at [Setting lifecycle configuration on a bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/how-to-set-lifecycle-configuration-intro.html)\. Do the following to remove noncurrent \(previous\) versions of an object and delete incomplete multipart uploads\. You don't need to do steps 6, 8, 9, 10\. 

   1. For step 5 choose **Apply to all objects in the bucket**\.

   1. For step 7 select **Permanently delete noncurrent versions of objects** and **Delete expired object delete markers or incomplete multipart uploads**\.

   1. For step 11 enter the numbers of days to wait before deleting noncurrent versions of an object\.

   1. For step 12 enter the number of days to wait before deleting incomplete multipart uploads\. 

## Console bucket settings<a name="su-console-bucket-settings"></a>

If you create the console bucket with the Amazon Lookout for Vision console, we enable the following settings on the console bucket\.
+ [Versioning](https://docs.aws.amazon.com/AmazonS3/latest/userguide/manage-versioning-examples.html) of objects in the console bucket\.
+ [Server\-side encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/default-bucket-encryption.html) of objects in the console bucket\.
+ [A lifecycle configuration](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.html) for the deletion of noncurrent objects \(30 days\) and incomplete multipart uploads \(3 days\)\. 
+ [Block public access](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-control-block-public-access.html) to the console bucket\.