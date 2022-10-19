# Using an Amazon Sagemaker Ground Truth job<a name="create-dataset-groundtruth-labeling-lob"></a>

Labeling images can take significant time\. For example, it can take 10s of seconds to accurately draw a mask around an anomaly\. If you have 100s of images, it might take several hours to label them\. As an alternative to labeling the images yourself, consider using Amazon SageMaker Ground Truth\. 

With Amazon SageMaker Ground Truth, you can use workers from either Amazon Mechanical Turk a vendor company that you choose, or an internal, private workforce to create a labeled set of images\. For more information, see [Use Amazon SageMaker Ground Truth to Label Data](https://docs.aws.amazon.com/sagemaker/latest/dg/sms.html)\. 

There is a cost for using Amazon Mechanical Turk\. Also, It might take several days to complete an Amazon Ground Truth labeling job\. If cost is an issue, or if you need to train your model quickly, we recommend that you use the Amazon Lookout for Vision console to [label](model-labelling-overview.md) your images\.

You can use an Amazon SageMaker Ground Truth labeling job to label images suitable for images classification models and image segmentation models\. After the job completes, you use the output manifest file to create an Amazon Lookout for Vision dataset\.

## Image classification<a name="create-dataset-groundtruth-labeling-lob-classification"></a>

To label images for an image classification model, create a labeling job for an [Image Classification \(Single Label\)](https://docs.aws.amazon.com/sagemaker/latest/dg/sms-image-classification.html) task\.

## Image segmentation<a name="create-dataset-groundtruth-labeling-lob-segmentation"></a>

To label images for an image segmentation model, create a labeling job for an Image Classification \(Single Label\) task\. Then, [chain](https://docs.aws.amazon.com/sagemaker/latest/dg/sms-reusing-data.html) the job to create a labeling job for an [Image Semantic Segmentation task](https://docs.aws.amazon.com/sagemaker/latest/dg/sms-semantic-segmentation.html)\. 

You can also use a labeling job to create a partial manifest file for an image segmentation model\. For example, you can classify images with an Image Classification \(Single Label\) task\. After creating a Lookout for Vision dataset with the job output, use the Amazon Lookout for Vision console to add segmentation masks and anomaly labels to the dataset images\.

## Labeling images with Amazon SageMaker Ground Truth<a name="create-dataset-groundtruth-labeling-lob-procedure"></a>

The following procedure shows how to label images with Amazon SageMaker Ground Truth image labeling tasks\. The procedure creates an image classification manifest file and optionally chains the image labeling task to create an image segmentation manifest file\. If you want your project to have a separate test dataset, repeat this procedure to create the manifest file for the test dataset\. 

**To label images with Amazon SageMaker Ground Truth \(Console\)**

1. Create a Ground Truth job for an *Image Classification \(Single Label\)* task by following the instructions at [Create a Labeling Job \(Console\)](https://docs.aws.amazon.com/sagemaker/latest/dg/sms-create-labeling-job-console.html)\.

   1. For step 10, choose **Image** from the **Task category** dropdown menu, and choose **Image Classification \(Single Label\)** as the task type\. 

   1. For step 16, in the **Image classification \(Single Label\) labeling tool** section, add two labels: **normal** and **anomaly**\.

1. Wait until the workforce finishes classifying your images\. 

1. If you are creating a dataset for an image segmentation model, do the following\. Otherwise go to to step 4\.

   1. In the Amazon SageMaker Ground Truth console, open the **Labeling jobs** page\.

   1. Choose the job you previously created\. This enables the **Actions** menu\.

   1. From the **Actions** menu, choose **Chain**\. The job details page opens\.

   1. In **task type**, choose **semantic segmentation**\. 

   1. Choose **Next**\.

   1. In the **Semantic segmentation labeling tool** section, add anomaly labels for each type of anomaly that you want your model to find\.

   1. Choose **Create**\.

   1. Wait until the workforce labels your images\.

1. Open the Ground Truth console and open the **Labeling jobs** page\.

1. If you are creating an image classification model, choose the job you created in step 1\. If you are creating an image segmentation model, choose the job created in step 3\. 

1. In **Labeling job summary** open the S3 location in **Output dataset location**\. Note the manifest file location, which should be `s3://output-dataset-location/manifests/output/output.manifest`\.

1. Repeat this procedure if you want to create a manifest file for a test dataset\. Otherwise, follow the instructions at [Creating the dataset](#create-dataset-groundtruth-labeling-lob-lookoutvision-console) to create a dataset with the manifest file\. 

## Creating the dataset<a name="create-dataset-groundtruth-labeling-lob-lookoutvision-console"></a>

Use this procedure to create a dataset in a Lookout for Vision project with the manifest file that you noted in step 6 of [ Labeling images with Amazon SageMaker Ground Truth](#create-dataset-groundtruth-labeling-lob-procedure)\. The manifest file creates the training dataset for a single dataset project\. If you want your project to have a separate test dataset, you can run another Amazon SageMaker Ground Truth job to create a manifest file for the test dataset\. Or you can [create](manifest-files.md) the manifest file yourself\. You can also import images to your test dataset from an Amazon S3 bucket or from your local computer\. \(The images might need labeling before you can train the model\)\. 

This procedure assumes that your project doesn't have any datasets\.

**To create a dataset with Lookout for Vision \(Console\)**

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

   1. For **\.manifest file location**, enter the location of the manifest file you noted in step 6 of [ Labeling images with Amazon SageMaker Ground Truth](#create-dataset-groundtruth-labeling-lob-procedure)\.

------
#### [ Separate training and test datasets ]

   1. Choose **Create a training dataset and a test dataset**\.

   1. In the **Training dataset details** section, choose **Import images labeled by SageMaker Ground Truth**\.

   1. In **\.manifest file location**, the location of the manifest file you noted in step 6 of [ Labeling images with Amazon SageMaker Ground Truth](#create-dataset-groundtruth-labeling-lob-procedure)\. 

   1. In the **Test dataset details** section, choose **Import images labeled by SageMaker Ground Truth**\.

   1. In **\.manifest file location**, the location of the manifest file you noted in step 6 of [ Labeling images with Amazon SageMaker Ground Truth](#create-dataset-groundtruth-labeling-lob-procedure)\. Remember that you need a separate manifest file for the test dataset\.

------

1. Choose **Submit**\. 

1. Follow the steps in [Training your model](model-train.md) to train your model\.