# Prepare example images<a name="su-prepare-example-images"></a>

Amazon Lookout for Vision provides example images of circuit boards that AWS customers can use to learn how to create, test, and use an [image classification](understanding.md#ud-image-classification) model\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/lookout-for-vision/latest/developer-guide/images/prediction.jpg)

You can copy the images from the [https://github\.com/aws\-samples/amazon\-lookout\-for\-vision](https://github.com/aws-samples/amazon-lookout-for-vision) GitHub repository\. The images are in the `circuitboard` folder\. 

The `circuitboard` folder has the following folders\.
+ `train` – Images you can use in a training dataset\.
+ `test` – Images you can use in a test dataset\.
+ `extra_images` – Images you can use to run a trial detection or to try out your trained model with the [DetectAnomalies](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_DetectAnomalies) operation\. 

The `train` and `test` folders each have a subfolder named `normal` \(contains images that are normal\) and a subfolder named `anomaly` \(contains images with anomalies\)\. 

**Note**  
Later, when you create a dataset with the console, Amazon Lookout for Vision can use the folder names \(`normal` and `anomaly`\) to automatically label the images\. For more information, see [Creating a dataset using images stored in an Amazon S3 bucket](create-dataset-s3.md)\.

**To prepare the dataset images**

1. Clone the [https://github\.com/aws\-samples/amazon\-lookout\-for\-vision](https://github.com/aws-samples/amazon-lookout-for-vision) repository to your computer\. For more information, see [Cloning a repository](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/cloning-a-repository)\. 

1. Create an Amazon S3 bucket\. For more information, see [How do I create an S3 Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-bucket.html)\. 

1. At the command prompt, enter the following command to copy the dataset images from your computer to your Amazon S3 bucket\.

   ```
   aws s3 cp --recursive your-repository-folder/circuitboard s3://your-bucket/circuitboard
   ```