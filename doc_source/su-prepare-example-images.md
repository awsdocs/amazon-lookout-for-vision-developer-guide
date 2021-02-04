--------

Amazon Lookout for Vision is in preview release and is subject to change\.

--------

# Step 5: \(Optional\) Prepare example images<a name="su-prepare-example-images"></a>

Amazon Lookout for Vision provides example images of circuit boards that AWS customers can use to learn how to create, test, and use a model\.

You can copy the images from the source Amazon S3 bucket \(that is, `s3://circuitboarddataset/`\) to the console bucket or another Amazon S3 bucket in your account\. For information about the console bucket, see [Step 3: Create the console bucket](su-create-console-bucket.md)\.

The `circuit_board` folder has the following folders\.
+ `train` – Images you can use in a training dataset\.
+ `test` – Images you can use in a test dataset\.
+ `extra_images` – Images you can use to run a trial detection or to try out your trained model\. 

The `train` and `test` folders each have a subfolder named `normal` \(contains images that are normal\) and a subfolder named `anomaly` \(contains images with anomalies\)\. 

**Note**  
Later, when you create a dataset, Amazon Lookout for Vision can use the folder names \(`normal` and `anomaly`\) to automatically label the images\. For more information, see [Creating a dataset using images stored in an Amazon S3 bucket](create-dataset-s3.md)\.

To try out your trained model with the [DetectAnomalies](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_DetectAnomalies) operation, you can download and use example images from the `extra_images` folder\. 

**To prepare the dataset images**

1. Create an Amazon S3 bucket\. For more information, see [How do I create an S3 Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-bucket.html)\. Alternatively, you can use the console bucket\. For more information, see [Step 3: Create the console bucket](su-create-console-bucket.md)\.

1. Open a command prompt and enter the following command to copy the images to your bucket\.

   ```
   aws s3 cp s3://circuitboarddataset/circuit_board/ s3://your bucket/circuit_board/ --recursive
   ```

1. Download the `extra_images` folder to your computer\.

   1. On your computer, create a folder named `extra_images`\.

   1. Open a command prompt and enter the following command to download the `extra_images` folder to your local computer\. 

      ```
      aws s3 cp s3://circuitboarddataset/circuit_board/extra_images/ extra_images/ --recursive
      ```

For more information, see [Getting Started with the Amazon Lookout for Vision console](getting-started.md) and [Getting started with the AWS SDK](getting-started-sdk.md)\.