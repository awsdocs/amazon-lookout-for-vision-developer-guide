# Creating a dataset with a manifest file \(console\)<a name="create-dataset-use-manifest"></a>

The following procedure shows you how to create a training or test dataset by importing an SageMaker format manifest file that is stored in an Amazon S3 bucket\. 

After you create the dataset, you can add more images to the dataset, or add labels to images\. For more information, see [Updating your dataset](edit-dataset.md)\.<a name="create-dataset-procedure-manifest-file"></a>

**To create a dataset using an SageMaker Ground Truth format manifest file \(console\)**

1. Create, or use an existing, Amazon Lookout for Vision compatible SageMaker Ground Truth format manifest file\. For more information, see [Creating a manifest file](manifest-files.md)\. 

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In an Amazon S3 bucket, [create a folder](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/create-folder.html) to hold your manifest file\. 

1. [Upload your manifest file](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/upload-objects.html) to the folder that you just created\.

1. In the Amazon S3 bucket, create a folder to store your images\.

1. Upload your images to the folder you just created\.
**Important**  
The `source-ref` field value in each JSON line must map to images in the folder\. 

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. Choose **Get started**\. 

1. In the left navigation pane, choose **Projects**\.

1. Choose the project that you want to add to use with the manifest file\.

1. In the **How it works** section, choose **Create dataset**\.

1. Choose the **Single dataset** tab or the **Separate training and test datasets** tab and follow the steps\.

------
#### [ Single dataset ]

   1. Choose **Create a single dataset**\.

   1. In the **Image source configuration** section, choose **Import images labeled by SageMaker Ground Truth**\.

   1. For **\.manifest file location**, enter the location of your manifest file\.

------
#### [ Separate training and test datasets ]

   1. Choose **Create a training dataset and a test dataset**\.

   1. In the **Training dataset details** section, choose **Import images labeled by SageMaker Ground Truth**\.

   1. In **\.manifest file location**, enter the location of your training manifest file\.

   1. In the **Test dataset details** section, choose **Import images labeled by SageMaker Ground Truth**\.

   1. In **\.manifest file location**, enter the location of your test manifest file\.
**Note**  
Your training and test datasets can have different image sources\.

------

1. Choose **Submit**\. 

1. Follow the steps in [Training your model](model-train.md) to train your model\.

Amazon Lookout for Vision creates a dataset in the Amazon S3 bucket `datasets` folder\. Your original `.manifest` file remains unchanged\.