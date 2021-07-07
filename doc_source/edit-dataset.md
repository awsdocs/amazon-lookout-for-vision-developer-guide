# Editing your dataset<a name="edit-dataset"></a>

After you create a dataset, you should make sure the images are correctly labeled\. You can also enhance the quality of your model by adding more images\. If you have created a test dataset, adding more images can increase the accuracy of your model's performance metrics\.

**Note**  
If you're following the Getting Started instructions, you don't need to edit your training and test datasets before creating your first model\.

Train your model after editing your datasets\.

**Topics**
+ [Labeling your images](#model-label)
+ [Adding more images](#add-more-images-dataset)

## Labeling your images<a name="model-label"></a>

You use the Lookout for Vision console to label images in a dataset as **normal** or **anomaly**\. This prepares the dataset for training so that the model can learn to recognize anomalies in new images\. Unlabeled images aren’t used to train your model\.

**Note**  
If you've just completed [Creating your dataset](model-create-dataset.md), the console should currently show your model dashboard and you don't need to do steps 1 \- 4\.

**To label your images \(console\)**

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. In the left navigation pane, choose **Projects**\.

1. In the **Projects** page, choose the project that you want to use\. 

1. In the left navigation pane of you project dashboard, choose **Dataset**\.

1.  If you have separate training and test datasets, choose the tab for the dataset that you want to use\.

1. Choose **Add labels**\.

1. Choose **Select all images on this page**\.

1. If the images are normal, choose **Classify as normal**, otherwise choose **Classify as abnormal**\. A label appears underneath each picture\.

1. If you need to change the label for an image, do the following:

   1. Choose **Anomaly** or **Normal** under the image\.

   1. If you can't determine the correct label for an image, magnify the image by choosing the image in the gallery\.
**Note**  
You can filter image labels by choosing the desired label, or label state, in the **Filters** section\.

1. Repeat steps 7\-9 on each page as necessary until all the images in the dataset have been labeled correctly\.

1. Choose **Save changes**\.

## Adding more images<a name="add-more-images-dataset"></a>

You can add more images to your datasets by uploading images from your local computer\. To add more labeled images with the SDK, use the [UpdateDatasetEntries](https://docs.aws.amazon.com/lookout-for-vision/latest/APIReference/API_UpdateDatasetEntries) operation\.

**To add more images to your dataset \(console\)**

1. Choose **Actions** and select the dataset that you want to add images to\. 

1. Choose the images you want to upload to the dataset\. You can drag the images or choose the images that you want to upload from your local computer\. You can upload up to 30 images at a time\.

1. Choose **Upload images**\.

1. Choose **Save changes**\.

When you are done adding more images, you need to label them so that they can be used to train the model\. For more information, see [Labeling your images](#model-label)\.