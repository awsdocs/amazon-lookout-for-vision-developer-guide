# Classifying images \(console\)<a name="model-label"></a>

You use the Lookout for Vision console to classify images in a dataset as **normal** or an **anomaly**\. Unclasified images aren’t used to train your model\.

If you're creating an image segmentation model, skip this procedure and do [Segmenting images \(console\)](segment-image.md), which includes steps to classify images\.

**Note**  
If you've just completed [Creating your dataset](model-create-dataset.md), the console should currently show your model dashboard and you don't need to do steps 1 \- 4\.

**To classify your images \(console\)**

1. Open the Amazon Lookout for Vision console at [ https://console\.aws\.amazon\.com/lookoutvision/]( https://console.aws.amazon.com/lookoutvision/)\.

1. In the left navigation pane, choose **Projects**\.

1. In the **Projects** page, choose the project that you want to use\. 

1. In the left navigation pane of your project, choose **Dataset**\.

1. If you have separate training and test datasets, choose the tab for the dataset that you want to use\.

1. Choose **Start labeling**\.

1. Choose **Select all images on this page**\.

1. If the images are normal, choose **Classify as normal**, otherwise choose **Classify as anomaly**\. A label appears underneath each picture\.

1. If you need to change the label for an image, do the following:

   1. Choose **Anomaly** or **Normal** under the image\.

   1. If you can't determine the correct label for an image, magnify the image by choosing the image in the gallery\.
**Note**  
You can filter image labels by choosing the desired label, or label state, in the **Filters** section\.

1. Repeat steps 7\-9 on each page as necessary until all the images in the dataset have been labeled correctly\.

1. Choose **Save changes**\.

1. If you've finished labeling your images, you can [train](model-train.md) your model\.