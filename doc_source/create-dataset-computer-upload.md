--------

Amazon Lookout for Vision is in preview release and is subject to change\.

--------

# Creating a dataset using images stored on your local computer<a name="create-dataset-computer-upload"></a>

You can create a dataset by using images that are loaded directly from your computer\. You can upload up to 30 images at a time\. In this procedure, you can create a single dataset project, or a project with separate training and test datasets\.

**Note**  
If you've just completed [Creating your project](model-create-project.md), the console should show your project dashboard and you don't need to do steps 1 \- 3\.

**To create a dataset using images on a local computer \(console\)**

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. In the left navigation pane, choose **Projects**\.

1. In the **Projects** page, choose the project to which you want to add a dataset\. 

1. On the project details page, choose **Create dataset**\.

1. Choose the **Single dataset** tab or the **Separate training and test datasets** tab and follow the steps\.

------
#### [ Single dataset ]

   1. Choose **Create a single dataset**\.

   1. In the **Image source configuration** section, choose **Upload images from your computer**\.

   1. Choose **Create dataset**\. 

   1. On the dataset page, choose **Add images**\. 

   1. Choose the images you want to upload into the dataset from your computer files\. You can drag the images or choose the images that you want to upload from your local computer\.

   1. Choose **Upload images**\.

------
#### [ Separate training and test datasets ]

   1. Choose **Create a training dataset and a test dataset**\.

   1. In the **Training dataset details** section, choose **Upload images from your computer**\.

   1. In the **Test dataset details** section, choose **Upload images from your computer**\.
**Note**  
Your training and test datasets can have different image sources\.

   1. Choose **Create dataset**\. A dataset page appears with a **Training** tab and a **Test** tab for the respective datasets\. 

   1. Choose **Actions** and then choose **Add images to training dataset**\.

   1. Choose the images you want to upload to the dataset\. You can drag the images or choose the images that you want to upload from your local computer\.

   1. Choose **Upload images**\.

   1. Repeat steps 6e \- 6g\. For step 6e, choose **Actions** and then choose **Add images to test dataset**\.

------

1. Follow the steps in [Labeling your images](edit-dataset.md#model-label) to label your images\.